# AVANCEMENT.md — Contract Clause Review Assistant

> Journal factuel des travaux. `CONTEXT.md` contient le périmètre et les décisions durables ; ce fichier suit les étapes effectuées, les preuves et les tâches suivantes.

**Dernière mise à jour : 17 octobre 2026.**

## 1. État actuel

- **Échéance :** 23 octobre 2026, GitHub + PowerPoint (10 min + 5 min de questions).
- **Dépôt :** [Charles-Scannavino/Projet_Deep_Learning](https://github.com/Charles-Scannavino/Projet_Deep_Learning), privé, branche `main`. Accès à la connexion GitHub vérifié.
- **État initial vérifié du dépôt :** un `README.md` et un commit « Initial commit » du 16 octobre 2026.
- **Équipe :** Charles Scannavino, Jacques et Khadidja ; branches `Jacques` et `Khadidja` créées pour le travail collaboratif.
- **Documents :** `README.md`, `CONTEXT.md` et `AVANCEMENT.md` disponibles et actualisés.
- **Notebooks :** `notebooks/Projet_DL_NLP.ipynb` (32 cellules) et `notebooks/colab.ipynb` (6 cellules, dont la copie du checkpoint sur Google Drive) publiés sur `main`, commit `b0a3f33`. Certaines sorties enregistrées sont anciennes et ne prouvent pas une exécution validée du nouveau projet.
- **Exécutions constatées (captures des 16–17 octobre) :** BERT entraîné complètement sur Colab, checkpoint sauvegardé sur Google Drive et Mac puis rechargé dans VS Code. Accuracy validation **84,24 %**, F1 macro validation **73,18 %**, accuracy test **84,48 %** sur 10 000 clauses. Audit descriptif par classe et recherche sémantique top-5 exécutés. Les fichiers modifiés localement restent à synchroniser sur GitHub.
- **Prochaine étape :** sauvegarder/synchroniser les notebooks modifiés, compléter le F1 macro du test et l'évaluation de la recherche sémantique, puis approfondir l'audit.

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
- [x] Charger LEDGAR (60 000 train, 10 000 validation, 10 000 test).
- [x] Vérifier les partitions, 100 catégories et la répartition des classes (11 classes sous 200, 3 sous 100).
- [ ] Contrôler valeurs manquantes, doublons et chevauchements entre partitions.
- [x] Afficher les percentiles tokenisés sur 2 000 exemples, mais tronqués à 512 tokens.
- [ ] Mesurer les longueurs sans troncature et quantifier les clauses dépassant 256 tokens.
- [x] Générer le graphique de distribution des classes dans le notebook.
- [ ] Enregistrer un bilan EDA autonome et les figures si nécessaires à la livraison.

### D — Classification BERT
- [ ] Mesurer éventuellement une baseline simple sur validation.
- [x] Configurer la tokenisation BERT (max_length=256) et le fine-tuning (lr=2e-5, batch=16, 2 époques).
- [ ] Tester une exécution réduite sur GPU Colab.
- [x] Entraîner le modèle sur Colab (7 500 étapes, GPU T4), sauvegarder sur Google Drive et Mac puis recharger les poids en local.
- [x] Mesurer accuracy val 0,8424, F1 macro val 0,7318, loss val 0,6119.
- [x] Exécuter une inférence sur les 10 000 clauses de test : accuracy 0,8448, ~12 min 32 s.
- [ ] Relever les métriques agrégées du test à partir des prédictions sauvegardées ; ne plus exploiter le test pour ajuster le modèle.

### E — Recherche sémantique
- [x] Encoder une banque de 10 000 clauses avec all-MiniLM-L6-v2.
- [x] Démontrer deux requêtes de recherche top-5 avec similarité cosinus.
- [ ] Reconstituer une banque fondée sur train ou un corpus indépendant, plutôt que test.
- [ ] Examiner la pertinence et les échecs ; si des annotations sont disponibles, mesurer Recall@k ou MRR.

### F — Audit et livraison
- [x] Calculer le rapport par classe et les confusions principales.
- [ ] Compléter l'analyse des échecs, de la troncature et des limites.
- [ ] Décrire les biais observables et les limites juridiques du système.
- [ ] Finaliser la documentation et les commandes de reproduction.
- [ ] Préparer le PowerPoint et vérifier le repository final.

## 3. Résultats vérifiés dans ce dépôt

| Composante | État | Preuve / emplacement |
| --- | --- | --- |
| Accès au dépôt | Vérifié | Repository privé et branche `main` accessibles |
| Documents de suivi | À jour | `README.md`, `CONTEXT.md`, `AVANCEMENT.md` |
| Notebooks sources | Publiés, non encore validés entièrement | `notebooks/Projet_DL_NLP.ipynb`, `notebooks/colab.ipynb` ; commit `b0a3f33` |
| Dataset et exploration | Exécutés sur Mac | 60k/10k/10k, 100 classes, graphiques et classes rares |
| Baseline | Classe majoritaire sur test | Accuracy 0,058 ; consultation précoce à documenter |
| Fine-tuning BERT | Terminé, checkpoint rechargé | Accuracy val 0,8424 ; F1 macro val 0,7318 ; loss val 0,6119 |
| Évaluation test | Prédictions réalisées | Accuracy test 0,8448, 10 000 clauses ; F1 agrégé à calculer |
| Recherche sémantique | Prototype exécuté | Banque de 10 000 clauses issues du test ; 2 requêtes top-5 ; pertinence quantitative non évaluée |
| Audit | Première analyse réalisée | F1 par classe et confusions, sans audit approfondi |
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

### 17 octobre 2026 — Résultats des exécutions Colab et VS Code

- **Preuves :** captures d'écran des sorties des notebooks exécutés par l'utilisateur. Les dernières modifications et sorties locales ne sont pas encore confirmées comme publiées sur GitHub.
- **GPU T4 / entraînement complet :** 2 époques, 7 500 étapes, `bert-base-uncased`, batch 16, longueur 256, LR 2e-5. Après époque 1 : accuracy validation 0,8246, F1 macro validation 0,6996. Après époque 2 : accuracy validation **0,8424**, F1 macro validation **0,7318**, loss validation **0,6119**.
- **Checkpoint :** `bert-ledgar.zip` téléchargé sur Mac, sauvegarde Drive confirmée, archive extraite en `models/bert-ledgar/` et chargée en local avec 100 classes ; sanity check sur 100 clauses de validation : accuracy 0,77.
- **EDA :** 60 000 train, 10 000 validation, 10 000 test ; 100 classes ; `Governing Laws` 3 167 occurrences en train, `Books` 23 ; 11 classes sous 200, 3 classes sous 100. Le calcul existant des longueurs tronque à 512 tokens et doit être rectifié pour analyser les longues clauses.
- **Mini-BERT de diagnostic en local :** entraînement sur 2 000 clauses, validation sur 500, une époque ; accuracy validation 0,2300 et F1 macro validation 0,0440. Test du mini-modèle exécuté prématurément : accuracy 0,218, F1 macro 0,0396. Les valeurs ne sont pas celles du BERT final.
- **Prédictions du BERT final sur test :** 10 000 clauses en ~12 min 32 s, accuracy **0,8448**. Fichiers locaux `test_preds.npy` et `test_labels.npy`. La baseline majoritaire a également consulté le test (accuracy 0,058), donc documenter ce protocole ; ne pas utiliser test pour de nouvelles décisions d'entraînement.
- **Audit initial :** rapport précision/rappel/F1 par classe et confusions. F1 nul observé pour `Applicable Laws`, `Assigns` et `Books` ; confusions `Tax Withholdings` → `Withholdings` (72), `Applicable Laws` → `Governing Laws` (42), `Defined Terms` → `Definitions` (40).
- **Recherche sémantique :** 10 000 clauses du test encodées par `all-MiniLM-L6-v2` (~1 min 20 s), top-5 pour deux requêtes juridiques avec réponses qualitativement plausibles ; la pertinence n'est pas encore évaluée quantitativement.
- **À faire :** sauvegarder et synchroniser les deux notebooks corrigés, éliminer `!import datasets` et les anciennes affirmations chiffrées, calculer le F1 macro du test sur prédictions déjà générées, utiliser une banque documentaire hors test, analyser les échecs et documenter la reproductibilité.

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
