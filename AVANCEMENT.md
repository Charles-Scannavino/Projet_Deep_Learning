# AVANCEMENT.md — Contract Clause Review Assistant

> Journal factuel des travaux. `CONTEXT.md` contient le périmètre et les décisions durables ; ce fichier suit les étapes effectuées, les preuves et les tâches suivantes.

**Dernière mise à jour : 16 octobre 2026.**

## 1. État actuel

- **Échéance :** 23 octobre 2026, GitHub + PowerPoint (10 min + 5 min de questions).
- **Dépôt :** [Charles-Scannavino/Projet_Deep_Learning](https://github.com/Charles-Scannavino/Projet_Deep_Learning), privé, branche `main`. Accès à la connexion GitHub vérifié.
- **État initial vérifié du dépôt :** un `README.md` et un commit « Initial commit » du 16 octobre 2026.
- **Documents :** `CONTEXT.md` et `AVANCEMENT.md` créés pour définir le suivi.
- **Exécutions de ce repository :** pas encore de dataset chargé, modèle entraîné, résultat de classification, index de recherche ou audit vérifié dans ce dépôt.
- **Prochaine étape :** inspecter et adapter les deux notebooks de travail, puis lancer l'analyse des données.

## 2. Feuille de route

### A — Installation et cadrage
- [x] Identifier le sujet, les quatre exigences techniques et la date de livraison.
- [x] Vérifier l'accès au nouveau dépôt GitHub privé et sa branche `main`.
- [x] Créer les fichiers `CONTEXT.md` et `AVANCEMENT.md`.
- [ ] Vérifier/compléter `.gitignore`, `README.md` et les dépendances du projet.
- [ ] Préparer l'environnement Python et le GPU Google Colab ; consigner les versions utilisées.

### B — Préparation des notebooks
- [ ] Examiner `Charles_projet_DL_NLP(2).ipynb` et `colab (1)(1).ipynb` cellule par cellule.
- [ ] Retirer les sorties anciennes non reproduites dans ce dépôt et corriger les erreurs de variables/chemins.
- [ ] Définir la responsabilité de chaque notebook pour éviter les entraînements et tests redondants.
- [ ] Ajouter les fichiers validés au repository par des commits explicites.

### C — Données et exploration
- [ ] Charger les données indiquées par le notebook de l'enseignant.
- [ ] Vérifier partitions, colonnes, nombre de labels, langue et répartition des classes.
- [ ] Contrôler valeurs manquantes, doublons et chevauchements entre partitions.
- [ ] Étudier les longueurs de clauses et le risque de troncature.
- [ ] Enregistrer des figures et un court bilan EDA issus des nouvelles exécutions.

### D — Classification BERT
- [ ] Mesurer éventuellement une baseline simple sur validation.
- [ ] Préparer tokenizer, labels, seeds et hyperparamètres.
- [ ] Tester une exécution réduite sur GPU Colab.
- [ ] Entraîner le modèle complet et sauvegarder son checkpoint.
- [ ] Mesurer la performance sur validation et analyser les erreurs.
- [ ] Après sélection du modèle, réaliser l'évaluation finale sur test et consigner les métriques.

### E — Recherche sémantique
- [ ] Encoder une banque de clauses à l'aide d'un Sentence-Transformer.
- [ ] Construire l'index et démontrer une recherche Top-k sur des requêtes concrètes.
- [ ] Examiner la pertinence et les échecs ; si des annotations sont disponibles, mesurer Recall@k ou MRR.

### F — Audit et livraison
- [ ] Analyser les classes peu représentées, les confusions, les clauses longues et les erreurs.
- [ ] Décrire les biais observables et les limites juridiques du système.
- [ ] Finaliser la documentation et les commandes de reproduction.
- [ ] Préparer le PowerPoint et vérifier le repository final.

## 3. Résultats vérifiés dans ce dépôt

| Composante | État | Preuve / emplacement |
| --- | --- | --- |
| Accès au dépôt | Vérifié | Repository privé et branche `main` accessibles |
| Documents de suivi | Créés | `CONTEXT.md`, `AVANCEMENT.md` |
| Dataset et exploration | Non exécutés | À documenter |
| Baseline | Non exécutée | À documenter |
| Fine-tuning BERT | Non exécuté | À documenter |
| Évaluation finale | Non exécutée | À documenter |
| Recherche sémantique | Non exécutée | À documenter |
| Audit | Non exécuté | À documenter |
| PowerPoint | Non réalisé | À documenter |

Aucune valeur de performance n'est inscrite avant sa mesure vérifiable.

## 4. Journal chronologique

### 16 octobre 2026 — Cadrage et dépôt GitHub

- Le repository `Charles-Scannavino/Projet_Deep_Learning` est accessible via la connexion GitHub et contient initialement `README.md`.
- Le cadre de travail est défini à partir des exigences de l'enseignant : classification BERT/CamemBERT, pipeline de test, recherche Sentence-Transformer et audit.
- Les deux notebooks de travail sont identifiés pour examen ; ils ne sont pas encore intégrés au repository.
- Les documents `CONTEXT.md` et `AVANCEMENT.md` sont préparés comme référence et journal de progression.

## 5. Format des prochaines mises à jour

Après chaque étape significative, ajouter une entrée contenant :

- **Date et objectif**
- **Actions réellement exécutées**
- **Configuration, environnement et versions**
- **Preuves** (cellules, commandes, captures, artefacts ou commit)
- **Résultats**, y compris difficultés et limites
- **Correction ou décision**
- **Prochaine action**

Conserver les observations précédentes et distinguer clairement les tâches prévues des tâches réalisées.
