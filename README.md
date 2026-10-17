# Contract Clause Review Assistant — Projet Deep Learning

**Projet universitaire réalisé en équipe de trois** dans le cours *Deep Learning for Sequential Data and NLP* de Guillaume Saint-Cirgue (Albert School / Mines Paris-PSL).

## Équipe

- **Charles Scannavino**
- **Jacques**
- **Khadidja**

La répartition des travaux sera précisée une fois validée par l'équipe. Les contributions de chaque personne sont suivies dans Git.

## Sujet et livrables

Développer le cœur NLP d'un assistant de revue de clauses contractuelles pour une application de legal-tech :

1. Classification des clauses par fine-tuning de **BERT ou CamemBERT**.
2. Pipeline de **tokenisation, entraînement et évaluation** sur un jeu de test tenu à part.
3. **Recherche sémantique** de clauses similaires avec un Sentence-Transformer.
4. **Audit** des biais, modes de défaillance et limites d'utilisation.

**Échéance : 23 octobre 2026.** Livraison GitHub et présentation PowerPoint (10 minutes, puis 5 minutes de questions).

## Données et outils

Le projet utilise **LEDGAR** (LexGLUE), chargé depuis Hugging Face :

```python
from datasets import load_dataset
ledgar = load_dataset("coastalcph/lex_glue", "ledgar")
```

| Partition | Nombre de clauses |
| --- | ---: |
| Entraînement | 60 000 |
| Validation | 10 000 |
| Test | 10 000 |

Le dataset comporte **100 catégories de clauses**. Les modèles utilisés dans les notebooks sont `bert-base-uncased` (classification) et `sentence-transformers/all-MiniLM-L6-v2` (recherche sémantique).

## Notebooks et exécution

| Notebook | Fonction |
| --- | --- |
| [`notebooks/colab.ipynb`](notebooks/colab.ipynb) | Fine-tuning BERT sur Google Colab / GPU T4, validation, export du modèle et copie dans Google Drive. |
| [`notebooks/Projet_DL_NLP.ipynb`](notebooks/Projet_DL_NLP.ipynb) | Exploration LEDGAR, mini-entraînement de diagnostic, chargement du modèle BERT entraîné, évaluation, analyse des confusions et recherche sémantique. |

Les deux notebooks **contiennent des sorties des exécutions réalisées les 16 et 17 octobre 2026** et ont été synchronisés par le commit [`3e278a8`](https://github.com/Charles-Scannavino/Projet_Deep_Learning/commit/3e278a88d38a1a0dbd36181c553b2bbd83a1728b). Ils ne doivent pas être relancés sans examiner les cellules : certaines réentraînent le modèle ou consultent le jeu de test.

### Entraînement BERT — Google Colab

1. Ouvrir `notebooks/colab.ipynb` avec [Google Colab](https://colab.research.google.com/) ; sélectionner **GPU T4**.
2. Exécuter la vérification du GPU, l'installation des bibliothèques, le chargement de LEDGAR et la tokenisation.
3. Lancer la cellule `Trainer` **uniquement lorsqu'un nouvel entraînement est réellement souhaité** : `bert-base-uncased`, 100 classes, `max_length=256`, learning rate `2e-5`, batch size 16, 2 époques.
4. Après les 7 500 étapes, exécuter la sauvegarde locale `bert-ledgar/` et l'archive `bert-ledgar.zip` ; autoriser Google Drive puis copier le dossier vers `MyDrive/Projet_Deep_Learning/bert-ledgar/`.
5. La dernière cellule évalue **la validation** avec `tok["validation"]`, sans relancer l'entraînement. Elle ne constitue pas une seconde évaluation du jeu de test.

Les checkpoints Colab temporaires peuvent disparaître à la fermeture de la session. Le ZIP et la copie Drive permettent de **réutiliser les poids entraînés sans relancer `trainer.train()`**.

### Analyse du modèle — VS Code / Mac

1. Cloner le dépôt privé et ouvrir `notebooks/Projet_DL_NLP.ipynb` dans VS Code.
2. Utiliser un noyau Python disposant de `torch`, `transformers`, `datasets`, `evaluate`, `numpy`, `matplotlib`, `scikit-learn`, `sentence-transformers`, `accelerate` et `ipykernel`. L'exécution documentée a utilisé **Python 3.12.15** dans un environnement Conda `Projet_Deep_Learning`.
3. Pour charger BERT, extraire `bert-ledgar.zip` en `models/bert-ledgar/`. Ce répertoire est ignoré par Git. Adapter le chemin du checkpoint à l'emplacement du clone, plutôt que de supposer que les poids sont inclus dans GitHub.
4. Exécuter les sections **sélectivement** : exploration, chargement du checkpoint, analyse des résultats et recherche sémantique. Un mini-fine-tuning local figure également dans le notebook mais n'est pas nécessaire pour utiliser le modèle Colab.

Exemple de chargement depuis la racine du clone :

```python
from pathlib import Path
from transformers import AutoTokenizer, AutoModelForSequenceClassification

checkpoint = Path("models/bert-ledgar").resolve()
tokenizer = AutoTokenizer.from_pretrained(str(checkpoint), local_files_only=True)
model = AutoModelForSequenceClassification.from_pretrained(str(checkpoint), local_files_only=True)
model.eval()
```

**Attention aux limites du code actuel :** une cellule contient encore `!import datasets` (commande système ImageMagick, pas import Python) ; les statistiques de longueur tronquées à 512 tokens sous-estiment les très longues clauses ; la banque de recherche utilise actuellement le **split test**. Ces points sont consignés dans `AVANCEMENT.md` et doivent être corrigés/justifiés.

## Résultats obtenus

| Mesure | Résultat | Périmètre |
| --- | ---: | --- |
| Accuracy BERT | **84,24 %** | Validation, 10 000 clauses |
| F1 macro BERT | **73,18 %** | Validation, 10 000 clauses |
| Loss BERT | **0,6119** | Validation, après 2 époques |
| Accuracy BERT | **84,48 %** | Test, 10 000 clauses |
| F1 macro BERT (rapport par classe) | **environ 0,72** | Test ; rapport arrondi à deux décimales |

Le fine-tuning complet sur GPU T4 a duré environ **1 h 28** pour deux époques (7 500 étapes). Le checkpoint a été conservé sur Google Drive et téléchargé sur Mac. Les prédictions test ont été produites localement (~12 min 32 s). Les résultats sont tirés des sorties enregistrées dans les notebooks.

### Recherche sémantique et audit

- **Recherche :** `all-MiniLM-L6-v2`, 10 000 clauses encodées, similarité cosinus et deux démonstrations de recherche top-5. Les exemples sont qualitatifs : **aucun Recall@k ou MRR vérifié à ce stade**.
- **Audit initial :** rapports précision/rappel/F1 par catégorie et confusions ; faibles performances sur plusieurs catégories rares ou proches lexicalement. Une analyse approfondie des biais et des sous-groupes reste à faire.

**Précaution d'évaluation :** le split test a déjà été consulté pendant les expérimentations. Il ne doit pas servir à ajuster les hyperparamètres ou à choisir rétrospectivement le meilleur modèle. Le corpus actuel du moteur de recherche est constitué de clauses du test ; cette limitation doit être corrigée ou clairement distinguée de l'évaluation de classification.

## Organisation et suivi

```text
Projet_Deep_Learning/
├── .gitignore
├── README.md
├── CONTEXT.md
├── AVANCEMENT.md
└── notebooks/
    ├── Projet_DL_NLP.ipynb
    └── colab.ipynb
```

- [`CONTEXT.md`](CONTEXT.md) décrit les exigences, décisions et conventions durables.
- [`AVANCEMENT.md`](AVANCEMENT.md) consigne les expériences vérifiées, les preuves, les problèmes et les prochaines tâches.
- Les poids BERT, caches, fichiers volumineux et données locales ne doivent pas être poussés sur GitHub.

## Collaboration

- `main` : branche d'intégration du travail commun.
- `Jacques` et `Khadidja` : branches de travail dédiées aux deux collaborateurs.
- Chaque contribution est proposée via une pull request vers `main`, après synchronisation de la branche avec la version courante et vérification des fichiers modifiés.

Le système est un **prototype d'analyse documentaire** : ses sorties ne constituent pas un avis juridique, et tout usage dans un contrat réel nécessite une vérification humaine.
