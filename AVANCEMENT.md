# AVANCEMENT.md — Contract Clause Review Assistant

> Journal factuel des travaux. `CONTEXT.md` contient le périmètre et les décisions durables ; ce fichier suit les étapes effectuées, les preuves et les tâches suivantes.

**Dernière mise à jour : 16 octobre 2026.**

## 1. État actuel

- **Échéance :** 23 octobre 2026, GitHub + PowerPoint (10 min + 5 min de questions).
- **Dépôt :** [Charles-Scannavino/Projet_Deep_Learning](https://github.com/Charles-Scannavino/Projet_Deep_Learning), privé, branche `main`. Accès à la connexion GitHub vérifié.
- **État initial vérifié du dépôt :** un `README.md` et un commit « Initial commit » du 16 octobre 2026.
- **Équipe :** Charles Scannavino, Jacques et Khadidja ; branches `Jacques` et `Khadidja` créées pour le travail collaboratif.
- **Documents :** `README.md`, `CONTEXT.md` et `AVANCEMENT.md` disponibles et actualisés.
- **Notebooks :** `notebooks/Projet_DL_NLP.ipynb` (32 cellules) et `notebooks/colab.ipynb` (6 cellules, dont la copie du checkpoint sur Google Drive) publiés sur `main`, commit `b0a3f33`. Certaines sorties enregistrées sont anciennes et ne prouvent pas une exécution validée du nouveau projet.
- **Exécutions de ce repository :** entraînement BERT complet interrompu ; aucune nouvelle métrique finale, aucun modèle entraîné et sauvegardé, aucun index ou audit complet vérifiés à ce stade.
- **Prochaine étape :** reprendre l'entraînement BERT sur GPU Colab, consigner ses résultats réels, puis vérifier le checkpoint avant les analyses.

## 2. Feuille de route

### A — Installation et cadrage
- [x] Identifier le sujet, les quatre exigences techniques et la date de livraison.
- [x] Vérifier l'accès au nouveau dépôt GitHub privé et sa branche `main`.
- [x] Créer les fichiers `CONTEXT.md` et `AVANCEMENT.md`.
- [x] Créer `.gitignore` et documenter le projet et ses notebooks dans `README.md`.
- [ ] Fixer les dépendances du projet dans un fichier de configuration.
- [x] Sélectionner le GPU T4 dans Colab pour l'entraînement BERT (confirmation utilisateur).
- [ ] Consigner les versions des bibliothèques et vérifier l'environnement complet.

### B — Préparation des notebooks
- [x] Identifier et publier les deux notebooks originaux sous leurs noms exacts : `Projet_DL_NLP.ipynb` et `colab.ipynb`.
- [x] Ajouter une cellule de sauvegarde Google Drive au notebook `colab.ipynb` (6 cellules au total).
- [ ] Revoir et exécuter tous les blocs nécessaires des notebooks, cellule par cellule.
- [ ] Qualifier puis retirer/actualiser les sorties anciennes non reproduites ; corriger les erreurs de variables/chemins. Les sorties préexistantes sont encore présentes dans les fichiers versionnés.
- [ ] Définir la responsabilité de chaque notebook pour éviter les entraînements et tests redondants.
- [x] Publier les deux notebooks dans le dépôt principal (commit `b0a3f33`).
- [ ] Valider leur exécution complète après correction des cellules problématiques.

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
| Documents de suivi | À jour | `README.md`, `CONTEXT.md`, `AVANCEMENT.md` |
| Notebooks sources | Publiés, non encore validés entièrement | `notebooks/Projet_DL_NLP.ipynb`, `notebooks/colab.ipynb` ; commit `b0a3f33` |
| Dataset et exploration | Résultats non vérifiés dans cette série de travaux | À documenter |
| Baseline | Non exécutée | À documenter |
| Fine-tuning BERT | Tentative interrompue, entraînement complet non validé | Relancer et vérifier les deux époques |
| Évaluation finale | Non exécutée | À documenter |
| Recherche sémantique | Non exécutée | À documenter |
| Audit | Non exécuté | À documenter |
| PowerPoint | Non réalisé | À documenter |

Aucune valeur de performance n'est inscrite avant sa mesure vérifiable.

## 4. Journal chronologique

### 16 octobre 2026 — Cadrage et dépôt GitHub

- Le repository `Charles-Scannavino/Projet_Deep_Learning` est accessible via la connexion GitHub et contient initialement `README.md`.
- Le cadre de travail est défini à partir des exigences de l'enseignant : classification BERT/CamemBERT, pipeline de test, recherche Sentence-Transformer et audit.
- Les deux notebooks de travail sont identifiés pour examen ; ils ont ensuite été intégrés au repository.
- Les documents `CONTEXT.md` et `AVANCEMENT.md` sont préparés comme référence et journal de progression.

### 16 octobre 2026 — Configuration, notebooks et collaboration

- Nouveau clone du dépôt sur Mac, branche `main`, synchronisation vérifiée.
- Fichier `.gitignore` ajouté par commit local `3366489`.
- Notebooks copiés depuis le Mac et publiés sous les noms exacts `Projet_DL_NLP.ipynb` et `colab.ipynb` par commit local `b0a3f33`.
- Ajout dans `colab.ipynb` d'une cellule qui copie le modèle fine-tuné sur Google Drive. L'entraînement lancé précédemment a été interrompu ; le résultat complet reste à produire.
- README mis à jour pour présenter le projet, les deux notebooks et l'équipe de trois : Charles Scannavino, Jacques et Khadidja.
- Branches collaboratives `Jacques` et `Khadidja` créées sur GitHub à partir de `main`.
- Point d'attention : la dernière cellule de `colab.ipynb` utilise `tokenized["test"]` au lieu de `tok["test"]` et doit attendre la phase d'évaluation finale.
- Les résultats numériques présents dans les notebooks issus d'exécutions anciennes ne sont pas considérés comme des scores revalidés pour cette remise.

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
