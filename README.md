# Contract Clause Review Assistant — Projet Deep Learning

**Projet universitaire réalisé en équipe de trois** dans le cours *Deep Learning for Sequential Data and NLP* de Guillaume Saint-Cirgue (Albert School / Mines Paris-PSL).

## Équipe

- **Charles Scannavino** : entraînement de la v1 (BERT) sur Colab
- **Jacques**
- **Khadidja** : évaluation, audit, entraînement et évaluation de la v2 (Legal-BERT)

## Sujet et livrables

Développer le cœur NLP d'un assistant de revue de clauses contractuelles pour une application de legal-tech :

1. Classification des clauses par fine-tuning de **BERT**.
2. Pipeline de **tokenisation, entraînement et évaluation** sur un jeu de test tenu à part.
3. **Recherche sémantique** de clauses similaires avec un Sentence-Transformer.
4. **Audit** des biais, modes de défaillance et limites d'utilisation.

**Échéance : 23 octobre 2026.** Livraison GitHub et présentation (10 minutes, puis 5 minutes de questions).

## Données

**LEDGAR** (benchmark LexGLUE), chargé depuis Hugging Face : `load_dataset("coastalcph/lex_glue", "ledgar")`. 80 000 clauses de contrats américains déposés auprès de la SEC, **100 types de clauses**, partitions 60 000 / 10 000 / 10 000 (entraînement / validation / test). Fort déséquilibre : 3 167 exemples pour *Governing Laws*, 23 pour *Books* (≈ 140 pour 1).

## Modèles

| Modèle | Rôle | Origine |
| --- | --- | --- |
| `bert-base-uncased` fine-tuné (**v1**) | Classification, 256 tokens, 2 époques, lr 2e-5 | Entraîné par l'équipe (`notebooks/colab.ipynb`) |
| `nlpaueb/legal-bert-base-uncased` fine-tuné (**v2, modèle retenu**) | Classification, 512 tokens, cross-entropy pondérée (1/√fréquence), 4 époques, lr 3e-5, meilleure époque sur la validation | Entraîné par l'équipe (`notebooks/colab_v2_legalbert.ipynb`) |
| `sentence-transformers/all-MiniLM-L6-v2` | Recherche sémantique (vecteurs de 384 dimensions) | Modèle public, utilisé tel quel |
| `SalmanAbbasi/lexglue-ledgar-distilbert` | **Référence externe** de comparaison uniquement | Modèle public, non entraîné par l'équipe |

## Télécharger le modèle BERT fine-tuné (v1)

Le modèle v1 entraîné (poids BERT et tokenizer, 100 classes LEDGAR) est disponible sous forme d'archive ZIP attachée à une **GitHub Release** du dépôt privé :

- **[Release `v1.0-bert-ledgar` — BERT LEDGAR, modèle fine-tuné](https://github.com/Charles-Scannavino/Projet_Deep_Learning/releases/tag/v1.0-bert-ledgar)**
- **[Télécharger directement `bert-ledgar.zip` (~406 Mo)](https://github.com/Charles-Scannavino/Projet_Deep_Learning/releases/download/v1.0-bert-ledgar/bert-ledgar.zip)**

Le fichier est hébergé dans les **Assets de la Release**, pas dans l'arborescence du code. L'accès au téléchargement nécessite un compte GitHub autorisé à lire ce repository privé.

Après téléchargement, extraire le dossier `bert-ledgar/` dans `models/bert-ledgar/` à la racine du clone. Le notebook principal le charge par le chemin relatif `../models/bert-ledgar`, sans nouvel entraînement. Les poids de la v2 (`bert-ledgar-v2.zip`) sont produits par `notebooks/colab_v2_legalbert.ipynb` sur Google Drive.

## Comment fonctionne le modèle et comment l'utiliser ?

**Classification des clauses.** Le modèle est un BERT préentraîné puis *fine-tuné* sur les **60 000 clauses d'entraînement LEDGAR**, réparties en **100 catégories**. Lorsqu'une clause en anglais est fournie, son tokenizer **WordPiece** la convertit en identifiants numériques (`input_ids`) et construit un masque d'attention (`attention_mask`). Le texte est limité à **256 tokens** pour la v1 et à **512 tokens** pour la v2 ; au-delà, la fin est tronquée. L'encodeur Transformer exploite la **self-attention bidirectionnelle** pour représenter les mots dans leur contexte. Sa tête de classification produit **100 logits** ; la catégorie prédite correspond au logit maximal. On peut appliquer un *softmax* pour obtenir des scores normalisés, mais ceux-ci ne doivent pas être interprétés comme des probabilités juridiques parfaitement fiables. Pendant le fine-tuning, la **cross-entropy**, la rétropropagation et l'optimiseur ont ajusté les poids du modèle pour rapprocher ses prédictions des catégories réelles. **Une fois le checkpoint sauvegardé, l'inférence ne nécessite aucun nouvel entraînement.**

**Tester une clause avec le modèle sauvegardé** (depuis la racine du clone, après extraction du ZIP en `models/bert-ledgar/`) :

```python
import torch
from pathlib import Path
from datasets import load_dataset
from transformers import AutoTokenizer, AutoModelForSequenceClassification

checkpoint = Path("models/bert-ledgar").resolve()
assert (checkpoint / "model.safetensors").is_file(), "Télécharger et extraire bert-ledgar.zip"

tokenizer = AutoTokenizer.from_pretrained(str(checkpoint), local_files_only=True)
model = AutoModelForSequenceClassification.from_pretrained(
    str(checkpoint), local_files_only=True
)
model.eval()

# Les noms et l'ordre des catégories correspondent au dataset utilisé pendant l'entraînement.
labels = load_dataset("coastalcph/lex_glue", "ledgar")["train"].features["label"].names

clause = "This Agreement shall be governed by the laws of the State of New York."
inputs = tokenizer(clause, return_tensors="pt", truncation=True, max_length=256)

with torch.inference_mode():
    logits = model(**inputs).logits
    scores = torch.softmax(logits, dim=-1)[0]
    pred_id = int(scores.argmax())

print("Catégorie prédite :", labels[pred_id])
print("Score softmax :", round(float(scores[pred_id]), 4))
```

Installer au minimum `torch`, `transformers` et `datasets`. Pour la v2, pointer `checkpoint` vers `models/bert-ledgar-v2` et utiliser `max_length=512`. La sortie illustre une **prédiction du modèle**, pas une qualification juridique définitive. La section 8 du notebook principal propose une démonstration complète (top-3 des types et clauses les plus proches).

**Recherche sémantique (second modèle distinct).** `sentence-transformers/all-MiniLM-L6-v2` encode chaque clause de la banque documentaire et la requête de l'utilisateur dans un espace vectoriel commun. Après normalisation, la **similarité cosinus** est calculée par produit scalaire, puis les `k` clauses les plus proches sont retournées avec leur score. Cette recherche ne requiert pas le checkpoint BERT de classification.

## Résultats (test, 10 000 clauses)

| Modèle | Accuracy | Macro-F1 | Types à F1 = 0 |
| --- | ---: | ---: | ---: |
| Baseline (classe majoritaire) | 5,8 % | — | — |
| BERT v1 | 84,48 % | 72,13 % | 9 |
| DistilBERT (référence externe) | 86,31 % | 77,60 % | 2 |
| **Legal-BERT v2** | **87,61 %** | **81,24 %** | **1** |

- **Choix de la v2 sur la validation**, avec une règle fixée avant de consulter le test : macro-F1 de validation 80,57 % contre 73,18 % pour la v1. Le test n'a été évalué qu'une fois pour la v2.
- **Significativité** (test de McNemar) : v2 contre v1, χ² = 112,5 ; v2 contre DistilBERT, χ² = 25,1 ; p < 0,001 dans les deux cas.
- **Fuite de données** : 282 clauses de test (2,8 %) figurent mot pour mot dans le train. Sur les 9 718 autres, la v2 obtient 87,31 % d'accuracy (section 9.3).

## Recherche sémantique

Banque de **60 000 clauses d'entraînement** (aucune clause de test) encodée avec all-MiniLM-L6-v2 ; requête → similarité cosinus → top-k. Évaluation qualitative sur des requêtes réelles (ex. : 5 résultats sur 5 de type *Governing Laws* pour une clause de droit de l'État de New York). Pas de Recall@k ni de MRR : il faudrait des jugements de pertinence annotés.

## Audit (sections 4, 6, 7 et 9 du notebook)

- **Par type de clause** : rapport complet sur les 100 types ; 9 types à F1 nul en v1 (quasi-synonymes et types rares), dont 8 récupérés par la v2.
- **Clauses longues** : la v1 perd 7,2 points de macro-F1 au-delà de 256 tokens ; la v2 (512 tokens) 0,7 point.
- **Calibration** : écart entre confiance moyenne et réussite réelle de +0,8 point pour la v1 (84,5 % contre 83,7 %) et de +5,3 points pour la v2 (92,5 % contre 87,2 %) : la v2 est surconfiante. Les ECE affichés (0,020 et 0,031) comparent chaque tranche à son centre ; ils restent à recalculer avec la confiance moyenne de chaque tranche, la définition standard.
- **Par famille de contrat** (estimée à partir des parties nommées dans la clause) : de 94,3 % (licences) à 73,9 % (baux) ; points faibles : baux et contrats de services.
- **Selon la forme** : pas de faiblesse sur les clauses en majuscules, avec des nombres ou avec des négations.

**Le système ne doit pas être utilisé pour** : décider seul ; trancher sur les types fragiles (*Assigns*, *Applicable Laws*) ; traiter d'autres langues ou d'autres droits ; traiter des contrats éloignés des dépôts SEC sans nouvelle évaluation ; juger la validité juridique d'une clause ; lire la confiance comme une probabilité exacte. **Le modèle propose, le juriste décide.**

## Notebooks

| Notebook | Contenu |
| --- | --- |
| [`notebooks/Projet_DL_NLP.ipynb`](notebooks/Projet_DL_NLP.ipynb) | **Notebook principal**, entièrement exécuté : exploration (1), pipeline et mini-test (2), évaluation test de la v1 (3), audit et comparaison externe (4), recherche sémantique (5), audit approfondi (6), v2 (7), démonstration (8), robustesse et fuite de données (9). |
| [`notebooks/colab.ipynb`](notebooks/colab.ipynb) | Entraînement de la v1 sur Colab (GPU T4). |
| [`notebooks/colab_v2_legalbert.ipynb`](notebooks/colab_v2_legalbert.ipynb) | Entraînement et sélection de la v2 sur Colab (GPU T4), avec ses sorties. |
| [`notebooks/audit_section6.ipynb`](notebooks/audit_section6.ipynb) | Brouillon autonome de la section 6 ; la version de référence est dans le notebook principal. |

Les prédictions sur le test sont versionnées pour que l'audit soit reproductible sans relancer l'inférence : `test_labels.npy`, `test_preds.npy` (v1), `test_preds_v2.npy` et `test_probs_v2.npy` (v2), `test_preds_distilbert.npy`.

## Reproduction

1. **Environnement** : Python 3.12 avec `torch`, `transformers`, `datasets`, `evaluate`, `scikit-learn`, `scipy`, `numpy`, `pandas`, `matplotlib`, `sentence-transformers`, `ipykernel`.
2. **Poids des modèles** (non versionnés, trop volumineux) : extraire `bert-ledgar.zip` (v1) dans `models/bert-ledgar/`. Les poids de la v2 (`bert-ledgar-v2.zip`) sont produits par `colab_v2_legalbert.ipynb` sur Google Drive.
3. **Notebook principal** : les sections d'audit se recalculent à partir des fichiers `.npy`, sans GPU. L'inférence complète de la section 3.2 prend environ 1 h 40 sur CPU.
4. **Entraînements** : à relancer uniquement sur Colab avec GPU T4 (88 min par version).

## Organisation

- [`CONTEXT.md`](CONTEXT.md) : exigences, décisions et conventions durables.
- [`AVANCEMENT.md`](AVANCEMENT.md) : journal des expériences, preuves et étapes.
- Branches : `main` (intégration), `Jacques` et `Khadidja` (travail), fusion par pull request.

Le système est un **prototype d'analyse documentaire** : ses sorties ne constituent pas un avis juridique, et tout usage sur un contrat réel nécessite une vérification humaine.
