# CONTEXT.md — Contract Clause Review Assistant

> Document de référence du projet. Il fixe le périmètre, les exigences et les règles de travail. Les exécutions, les résultats et les prochaines tâches figurent dans `AVANCEMENT.md`.

**Dernière mise à jour : 16 octobre 2026.**

## 1. Projet et échéance

- **Cours :** Deep Learning for Sequential Data and NLP (MSA-DAT07-01), Guillaume Saint-Cirgue.
- **Sujet :** développer le cœur NLP d'un assistant de revue de clauses contractuelles pour une entreprise européenne de legal-tech.
- **Repository :** [Charles-Scannavino/Projet_Deep_Learning](https://github.com/Charles-Scannavino/Projet_Deep_Learning), privé, branche principale `main`.
- **Équipe de trois :** Charles Scannavino, Jacques et Khadidja. Les branches de travail des deux collaborateurs sont respectivement `Jacques` et `Khadidja`. La répartition des tâches reste à préciser.
- **Livraison :** 23 octobre 2026 : dépôt GitHub et présentation PowerPoint (10 minutes de présentation, 5 minutes de questions).
- **Point d'entrée fourni par l'enseignant :** [notebook Google Colab](https://colab.research.google.com/drive/1rVOdhKNtL9qk-6ZjuFc0ZcQlTLfvaUjg#scrollTo=9FwDLJi7Yodz). Le dataset, ses caractéristiques et les modalités d'utilisation devront être confirmés dans les exécutions du projet.

## 2. Exigences de l'énoncé

1. **Classification :** fine-tuner BERT ou CamemBERT pour affecter une catégorie aux clauses contractuelles.
2. **Pipeline NLP :** tokeniser, entraîner et évaluer sur un jeu de test tenu à part.
3. **Recherche sémantique :** construire un index fondé sur un Sentence-Transformer pour retrouver des clauses similaires à une clause ou une requête en langage naturel.
4. **Audit :** identifier et expliquer les biais, les erreurs, les modes de défaillance, les usages appropriés et les limites du système.

Un chatbot génératif, un agent et une interface web ne sont pas des exigences identifiées de l'énoncé : ils ne sont pas prioritaires.

## 3. Méthode de travail

- **Source de référence :** suivre l'énoncé et le notebook de l'enseignant. Les modèles, jeux de données et packages utilisés sont cités avec leur provenance.
- **Notebooks actuellement versionnés :** `notebooks/Projet_DL_NLP.ipynb` (analyse, classification, recherche et audit) et `notebooks/colab.ipynb` (entraînement GPU et sauvegarde Google Drive). Le code et les sorties existantes doivent encore être vérifiés et testés avant validation des livrables.
- **Environnement :** Python et Hugging Face/PyTorch ; Google Colab avec GPU si nécessaire. Consigner versions et configuration réellement utilisées.
- **Reproductibilité :** fixer les seeds ; conserver les paramètres, la version des modèles, les partitions, les métriques et les commandes d'exécution.
- **Preuves :** aucune métrique, aucun modèle entraîné et aucun audit ne sont déclarés validés sans sortie d'exécution ou artefact correspondant.
- **Git :** commits explicites, changements réalisés sur le bon repository ; vérifier l'état distant avant toute modification. `main` accueille les contributions via pull requests depuis les branches de travail. Préserver l'historique et la traçabilité des contributions.

## 4. Protocole technique

### 4.1 Données et classification

- Inspecter les champs et les labels réels, la langue, les classes rares, les doublons et la distribution des longueurs.
- Utiliser les partitions entraînement / validation / test disponibles ; détecter leurs éventuels recouvrements.
- Choisir les hyperparamètres sur **validation uniquement**, puis procéder au test final avec le modèle fixé.
- Comparer à une baseline simple si possible (classe majoritaire et/ou TF-IDF + classifieur linéaire) : **recommandation méthodologique**, non exigence supplémentaire attribuée au professeur.
- Consigner accuracy, macro-F1, métriques par classe, confusions et exemples d'erreurs. Vérifier l'impact de la troncature des clauses longues.
- Enregistrer le modèle et le tokenizer en dehors de Git si leurs poids sont volumineux.

### 4.2 Recherche sémantique

- Construire des embeddings avec un Sentence-Transformer et indexer une banque de clauses identifiées.
- Retourner un top-k avec texte, identifiant et score de similarité.
- Documenter la composition de la banque pour éviter toute confusion entre apprentissage, recherche et test de généralisation.
- Évaluer qualitativement les résultats ; des mesures telles que Recall@k et MRR nécessitent des requêtes et jugements de pertinence vérifiés (**complément recommandé**).

### 4.3 Audit

- Étudier les erreurs observées et les écarts de performance entre classes ou sous-groupes effectivement disponibles.
- Examiner les effets du déséquilibre, des formulations ambiguës, des clauses longues, des nombres et des négations.
- Distinguer similarité textuelle et validité juridique. Le système ne remplace pas un juriste et ne doit pas produire de décision juridique autonome.
- Signaler les limites qui ne peuvent pas être mesurées avec les métadonnées du dataset.

## 5. Organisation documentaire

- `README.md` : présentation, installation et reproduction.
- `CONTEXT.md` : exigences, conventions et décisions durables.
- `AVANCEMENT.md` : tâches, résultats réels, blocages, preuves et prochaines étapes.
- `notebooks/` : notebooks effectivement revus et exécutés.
- `results/` : rapports légers, figures et métriques obtenues lors des exécutions.

La structure sera complétée au fur et à mesure des besoins ; ne pas publier de secrets, contrats confidentiels, caches ou poids volumineux.

## 6. Règles pour chaque session

1. Relire `CONTEXT.md` et `AVANCEMENT.md` et vérifier l'état du repository.
2. Présenter chaque étape : objectif → code → exécution → résultats → interprétation.
3. Corriger les notebooks de façon minimale et expliquer toute modification.
4. Mettre à jour `AVANCEMENT.md` après une tâche significative, avec ses preuves ; modifier `CONTEXT.md` seulement si une décision durable change.
5. Ne jamais présenter comme résultats du projet des métriques provenant d'autres exécutions non reproduites ou des valeurs attendues.
6. Préparer des livrables techniques compréhensibles et défendables lors de la soutenance.
