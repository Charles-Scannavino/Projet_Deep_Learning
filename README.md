# Contract Clause Review Assistant — Projet Deep Learning

**Projet universitaire réalisé en équipe de trois** dans le cadre du cours *Deep Learning for Sequential Data and NLP* (Guillaume Saint-Cirgue, Albert School / Mines Paris-PSL).

## Équipe

- **Charles Scannavino**
- **Jacques**
- **Khadidja**

La répartition précise des tâches sera indiquée lorsqu'elle aura été décidée collectivement.

## Objectif

Développer le cœur NLP d'un assistant de revue de clauses contractuelles pour une application de legal-tech. Le projet comporte quatre composantes :

1. **Classification** de clauses par fine-tuning de BERT ou CamemBERT.
2. **Pipeline complet** de tokenisation, d'entraînement et d'évaluation sur un jeu de test tenu à part.
3. **Recherche sémantique** de clauses similaires à l'aide d'un Sentence-Transformer.
4. **Audit des biais**, des erreurs et des limites d'utilisation du système.

**Livraison : 23 octobre 2026**, avec code GitHub et présentation PowerPoint (10 minutes de présentation, 5 minutes de questions).

## Données et modèles

Les notebooks utilisent le jeu de données **LEDGAR**, issu de **LexGLUE**, accessible depuis Hugging Face via :

```python
from datasets import load_dataset
ledgar = load_dataset("coastalcph/lex_glue", "ledgar")
```

Le code prévoit **100 catégories de clauses** et les partitions `train`, `validation` et `test`. Le chargement télécharge et met en cache les données au premier usage.

Modèles prévus dans les notebooks :
- **`bert-base-uncased`** pour la classification, avec fine-tuning.
- **`sentence-transformers/all-MiniLM-L6-v2`** pour la recherche sémantique.

Ces modèles préentraînés sont téléchargés à l'exécution ; les poids du modèle BERT fine-tuné ne sont pas stockés dans Git.

## Les deux notebooks du projet

| Fichier | Utilisation |
| --- | --- |
| [`notebooks/colab.ipynb`](notebooks/colab.ipynb) | Préparation des données, entraînement BERT sur Google Colab avec GPU, sauvegarde du checkpoint et copie vers Google Drive. |
| [`notebooks/Projet_DL_NLP.ipynb`](notebooks/Projet_DL_NLP.ipynb) | Exploration et visualisation de LEDGAR, expérimentation de classification, évaluation et audit, recherche sémantique. |

**Les notebooks sont des supports de travail en cours de vérification**, et non la preuve qu'un entraînement complet ou une évaluation finale a déjà été validé.

### Exécuter `colab.ipynb` sur Google Colab

1. Ouvrir [Google Colab](https://colab.research.google.com/) puis importer `notebooks/colab.ipynb`.
2. Choisir un environnement d'exécution **GPU T4** lorsqu'il est disponible.
3. Exécuter successivement la vérification GPU, l'installation et la préparation des données.
4. Vérifier la configuration du `Trainer` ; lancer ensuite le fine-tuning si les paramètres conviennent. Le code actuellement versionné utilise **2 époques** et un batch size de **16**.
5. **Seulement après la fin complète de l'entraînement**, exécuter la sauvegarde `bert-ledgar/`, puis sa copie vers `Google Drive/MyDrive/Projet_Deep_Learning/bert-ledgar/`.

**Attention : ne pas utiliser « Tout exécuter » pour l'instant.** La dernière cellule d'origine appelle `tokenized["test"]` au lieu de `tok["test"]` et accède prématurément au jeu de test. Elle devra être corrigée lors de la phase d'évaluation finale. Les checkpoints de `runs/` conservés uniquement dans la session Colab peuvent disparaître après déconnexion ; sauvegarder le modèle durablement.

### Ouvrir `Projet_DL_NLP.ipynb` dans VS Code

Cloner le projet :

```bash
git clone https://github.com/Charles-Scannavino/Projet_Deep_Learning.git
cd Projet_Deep_Learning
code .
```

Ouvrir `notebooks/Projet_DL_NLP.ipynb` dans VS Code avec l'extension Jupyter et un environnement Python disposant notamment de `datasets`, `transformers`, `torch`, `evaluate`, `scikit-learn`, `matplotlib` et `sentence-transformers`. L'environnement local sera documenté et figé lors des étapes de validation.

Exécuter d'abord les cellules d'exploration. **Ne pas exécuter sans vérification les cellules d'entraînement redondant ou d'évaluation sur `test`**. Pour réutiliser BERT, il faudra récupérer le checkpoint entraîné sur Colab et corriger son chemin local `bert-ledgar/`.

Pour une comparaison non biaisée, les paramètres sont choisis sur **validation** et le jeu **test** n'est utilisé qu'à la fin.

## Organisation du dépôt

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

- [`CONTEXT.md`](CONTEXT.md) : énoncé, choix et règles de travail.
- [`AVANCEMENT.md`](AVANCEMENT.md) : étapes réellement exécutées, résultats vérifiés et prochaines actions.

## Travail collaboratif

- **`main`** : branche d'intégration commune.
- **`Jacques`** : branche de travail de Jacques.
- **`Khadidja`** : branche de travail de Khadidja.

Chaque membre effectue ses commits sur sa branche de travail et propose une **pull request** vers `main`, avec une description des changements et des vérifications. Les droits d'accès au dépôt doivent être accordés séparément des branches.

Les contributions sont tracées par Git. Ne pas publier de secrets, de contrats confidentiels, de caches ou de poids de modèles volumineux. Les résultats annoncés doivent correspondre à des exécutions vérifiables.
