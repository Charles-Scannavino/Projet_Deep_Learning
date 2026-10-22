# AVANCEMENT.md — Contract Clause Review Assistant

> Journal factuel des travaux. `CONTEXT.md` contient le périmètre et les décisions durables ; ce fichier suit les étapes effectuées, les preuves et les tâches suivantes.

**Dernière mise à jour : 22 octobre 2026.**

## 1. État actuel

- **Échéance :** 23 octobre 2026, GitHub + PowerPoint (10 min + 5 min de questions).
- **Dépôt :** [Charles-Scannavino/Projet_Deep_Learning](https://github.com/Charles-Scannavino/Projet_Deep_Learning), privé, branche `main`. Accès à la connexion GitHub vérifié.
- **État initial vérifié du dépôt :** un `README.md` et un commit « Initial commit » du 16 octobre 2026.
- **Équipe :** Charles Scannavino, Jacques et Khadidja ; branches `Jacques` et `Khadidja` créées pour le travail collaboratif.
- **Documents :** `README.md`, `CONTEXT.md` et `AVANCEMENT.md` disponibles et actualisés.
- **Notebooks :** `notebooks/Projet_DL_NLP.ipynb` (**33 cellules**) et `notebooks/colab.ipynb` (**7 cellules**) publiés sous leurs noms exacts ; dernières versions locales synchronisées sur `main` par commit **`3e278a8`** du 17 octobre, avec code et sorties d'exécution. Des commentaires Markdown issus d'anciennes étapes restent à harmoniser.
- **Exécutions constatées (captures des 16–17 octobre) :** BERT entraîné complètement sur Colab, checkpoint sauvegardé sur Google Drive et Mac puis rechargé dans VS Code. Accuracy validation **84,24 %**, F1 macro validation **73,18 %**, accuracy test **84,48 %** sur 10 000 clauses. Audit descriptif par classe et recherche sémantique top-5 exécutés. Les deux versions avec sorties ont été synchronisées sur GitHub par commit `3e278a8`.
- **Checkpoint BERT téléchargeable :** GitHub Release [`v1.0-bert-ledgar`](https://github.com/Charles-Scannavino/Projet_Deep_Learning/releases/tag/v1.0-bert-ledgar) contenant `bert-ledgar.zip` (~406 Mo), accessible aux collaborateurs autorisés.
- **Prochaine étape (revue du 22 octobre) :** examiner puis intégrer sans perte les contributions de `Khadidja`, corriger la métrique ECE, fiabiliser protocole et dépendances, puis préparer la présentation. Voir le bilan mission par mission ci-dessous.

## 1 bis. Contrôle des quatre missions de CONTEXT.md — 22 octobre 2026

Vérification réalisée à partir des **sorties enregistrées** dans `notebooks/colab.ipynb` et `notebooks/Projet_DL_NLP.ipynb` sur la branche `main`, de la Release du checkpoint, ainsi que des travaux **non fusionnés** sur la branche `Khadidja`. Aucune nouvelle exécution expérimentale ni fusion de code n'a été effectuée lors de cette vérification. La réussite du test manuel en Terminal a été communiquée par l'utilisateur ; elle n'est pas encore enregistrée comme test automatisé dans le dépôt.

| Mission de `CONTEXT.md` | Statut sur `main` | Preuves vérifiées | Ce qu'il reste à réaliser / vérifier |
| --- | --- | --- | --- |
| **1. Classification BERT/CamemBERT** | **Réalisée** | `colab.ipynb` : `bert-base-uncased` fine-tuné sur 60 000 clauses LEDGAR, 100 classes, 2 époques, 7 500 étapes. Checkpoint `bert-ledgar.zip` publié en Release `v1.0-bert-ledgar`. | Pas de nouvel entraînement requis ; vérifier l'identité du modèle chargé dans toutes les démonstrations. |
| **2. Pipeline NLP (tokeniser, entraîner, évaluer sur test tenu à part)** | **Réalisée avec réserves méthodologiques** | Tokenisation WordPiece (`max_length=256`), `DataCollatorWithPadding`, `Trainer`, validation accuracy **0,8424** / F1 macro **0,731838**, inférence BERT final sur 10 000 clauses test : accuracy **0,8448**, rapport par classe et confusions. | Un mini-modèle et la baseline ont consulté le test prématurément. Ne plus utiliser le test pour ajuster le modèle ; vérifier doublons / chevauchements entre splits, nettoyer cellules historiques, fournir des dépendances figées, chemins portables et procédure reproductible. |
| **3. Recherche sémantique Sentence-Transformer** | **Prototype réalisé ; évaluation incomplète** | `Projet_DL_NLP.ipynb` sur `main` : `all-MiniLM-L6-v2`, embeddings normalisés, cosinus et deux requêtes top-5 sur 10 000 clauses. | Sur `main`, la banque documentaire provient du **test**. La branche `Khadidja` utilise **60 000 clauses du train** et contient des sorties de recherche, mais n'est **pas fusionnée**. Vérifier la pertinence des résultats et évaluer si possible avec jugements de pertinence, Recall@k/MRR ; afficher aussi des identifiants de clauses si un index documentaire exploitable est attendu. |
| **4. Audit : biais, erreurs, défaillances, usages et limites** | **Partiellement réalisée sur `main` ; approfondie sur branche non fusionnée** | `main` : F1 par classe, classes rares et confusions (ex. `Tax Withholdings` → `Withholdings`, 72 cas). Sur `Khadidja` : neuf classes au F1 nul, comparaison clauses ≤ 256 / > 256 tokens (**0,728 / 0,656** F1 macro), corrélation Spearman support train/F1 **0,500**, exemples d'erreurs, diagramme de calibration et limites d'usage juridique. | Revoir puis intégrer les résultats de `Khadidja`. **ECE annoncé 0,0195 à recalculer** : le code compare l'accuracy des bins à leur centre plutôt qu'à la confiance moyenne de chaque bin. Documenter explicitement les limites non mesurées (négations, chiffres, sous-populations indisponibles) et les usages à proscrire ; éviter les conclusions juridiques automatiques. |

**Bilan :** les quatre missions possèdent une implémentation ou des analyses réelles, mais **toutes ne sont pas finalisées sur `main`**. Missions 1 et 2 techniquement accomplies ; mission 3 opérationnelle sans évaluation systématique de pertinence ; mission 4 initiale sur `main`, plus aboutie mais non intégrée sur `Khadidja`. L'existence d'une sortie de cellule ne remplace pas une vérification de protocole.

**Éléments complémentaires vérifiés :**
- Le `README.md` explique désormais le fonctionnement de BERT et contient un exemple Python d'inférence (`ad6003c`).
- **Test manuel utilisateur du 22 octobre, hors notebook versionné :** sur une clause de confidentialité formulée pour la démonstration, le checkpoint local a prédit `Confidentiality` (**97,50 %** softmax), puis `Disclosures` (**0,46 %**) et `Publicity` (**0,15 %**). Cette seule clause ne constitue pas une nouvelle évaluation statistique ; les scores softmax ne sont pas des garanties juridiques.
- Sur `Khadidja`, le tableau BERT vs DistilBERT présente des valeurs du modèle externe directement renseignées dans le code : **comparaison non établie comme reproduite sous le même protocole**, à sourcer ou exécuter avant la soutenance.
- Les branches `main` et `Khadidja` ont divergé ; **ne pas écraser `AVANCEMENT.md`** lors d'une future fusion. Examiner les cellules, sorties et différences avant d'intégrer les contributions.
- Les fichiers `CONTEXT.md`, les notebooks et les modèles n'ont pas été modifiés par cette mise à jour documentaire.

### Priorités avant la remise du 23 octobre 2026

1. Réviser et intégrer de manière contrôlée les contributions de `Khadidja` ; corriger l'ECE et qualifier la comparaison externe.
2. Assainir la méthodologie test/retrieval et la reproductibilité (dépendances, chemins, sorties, doublons et chevauchements).
3. Préparer la soutenance de 10 minutes et ses preuves : pipeline, résultats, classes rares, limites juridiques ; vérifier la présentation et l'historique Git.

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
- [x] Exécuter et conserver les sorties des principales sections des deux notebooks (Colab et VS Code, 16–17 octobre).
- [ ] Séparer clairement les expériences de diagnostic, l'utilisation du modèle sauvegardé et la phase finale de test.
- [ ] Corriger les références chiffrées périmées dans les cellules Markdown et la commande `!import datasets` ; adapter le chemin absolu du checkpoint à un chemin portable. Les nouvelles sorties d'exécution sont maintenant versionnées.
- [ ] Définir la responsabilité de chaque notebook pour éviter les entraînements et tests redondants.
- [x] Publier les notebooks (commit `b0a3f33`), puis leurs dernières versions exécutées et sorties (commit **`3e278a8`**).
- [x] Vérifier l'entraînement Colab, l'usage du modèle sauvegardé en local, l'inférence test, l'audit initial et les démonstrations de recherche.
- [ ] Finaliser le nettoyage des cellules, la robustesse et la reproductibilité.

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
- [x] Générer un `classification_report` sur test : macro précision **0,74**, macro rappel **0,73**, macro F1 **0,72** (arrondis à deux décimales) ; weighted F1 **0,83**.
- [ ] Enregistrer les valeurs non arrondies, sans réentraîner ni ajuster le modèle sur test.

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
| Notebooks sources | Dernières versions exécutées publiées | `notebooks/Projet_DL_NLP.ipynb` (33 cellules), `notebooks/colab.ipynb` (7 cellules) ; commit **`3e278a8`** |
| Dataset et exploration | Exécutés sur Mac | 60k/10k/10k, 100 classes, graphiques et classes rares |
| Baseline | Classe majoritaire sur test | Accuracy 0,058 ; consultation précoce à documenter |
| Fine-tuning BERT | Terminé, checkpoint rechargé | Accuracy val 0,8424 ; F1 macro val 0,7318 ; loss val 0,6119 |
| Évaluation test | Prédictions et rapport par classe publiés | Accuracy 0,8448 ; macro-F1 0,72 (arrondi) ; weighted-F1 0,83 (arrondi), sur 10 000 clauses |
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

- **Preuves :** captures d'écran et sorties désormais présentes dans les notebooks poussés sur GitHub (`3e278a8`).
- **GPU T4 / entraînement complet :** 2 époques, 7 500 étapes, `bert-base-uncased`, batch 16, longueur 256, LR 2e-5. Après époque 1 : accuracy validation 0,8246, F1 macro validation 0,6996. Après époque 2 : accuracy validation **0,8424**, F1 macro validation **0,7318**, loss validation **0,6119**.
- **Checkpoint :** `bert-ledgar.zip` téléchargé sur Mac, sauvegarde Drive confirmée, archive extraite en `models/bert-ledgar/` et chargée en local avec 100 classes ; sanity check sur 100 clauses de validation : accuracy 0,77.
- **EDA :** 60 000 train, 10 000 validation, 10 000 test ; 100 classes ; `Governing Laws` 3 167 occurrences en train, `Books` 23 ; 11 classes sous 200, 3 classes sous 100. Le calcul existant des longueurs tronque à 512 tokens et doit être rectifié pour analyser les longues clauses.
- **Mini-BERT de diagnostic en local :** entraînement sur 2 000 clauses, validation sur 500, une époque ; accuracy validation 0,2300 et F1 macro validation 0,0440. Test du mini-modèle exécuté prématurément : accuracy 0,218, F1 macro 0,0396. Les valeurs ne sont pas celles du BERT final.
- **Prédictions du BERT final sur test :** 10 000 clauses en ~12 min 32 s, accuracy **0,8448**. Fichiers locaux `test_preds.npy` et `test_labels.npy`. La baseline majoritaire a également consulté le test (accuracy 0,058), donc documenter ce protocole ; ne pas utiliser test pour de nouvelles décisions d'entraînement.
- **Audit initial :** rapport précision/rappel/F1 par classe et confusions. F1 nul observé pour `Applicable Laws`, `Assigns` et `Books` ; confusions `Tax Withholdings` → `Withholdings` (72), `Applicable Laws` → `Governing Laws` (42), `Defined Terms` → `Definitions` (40).
- **Recherche sémantique :** 10 000 clauses du test encodées par `all-MiniLM-L6-v2` (~1 min 20 s), top-5 pour deux requêtes juridiques avec réponses qualitativement plausibles ; la pertinence n'est pas encore évaluée quantitativement.
- **État du rapport test :** macro-F1 **0,72** et weighted-F1 **0,83**, valeurs arrondies à deux décimales dans `classification_report`. Les sorties sont présentes dans le notebook versionné.
- **À faire :** éliminer `!import datasets` et harmoniser les affirmations Markdown, rendre portable le chemin du checkpoint, publier les dépendances, mesurer les longueurs sans troncature, utiliser une banque documentaire hors test, approfondir l'audit et évaluer le retrieval.

### 17 octobre 2026 — Synchronisation des notebooks exécutés et documentation

- Les versions de référence provenant de `Documents/Projets/Projet_Deep_Learning/Notebook/` ont été copiées dans le clone Git sous leurs **noms exacts**.
- Publication sur `main` par **commit `3e278a8`** (*Update notebooks with latest Colab and VS Code executions*) ; seules les deux copies sous `notebooks/Projet_DL_NLP.ipynb` et `notebooks/colab.ipynb` ont été suivies dans ce commit.
- Contenu vérifié sur GitHub : `Projet_DL_NLP.ipynb` possède **33 cellules**, dont les sorties de l'analyse LEDGAR, de l'inférence test, du rapport des 100 classes et des démonstrations sémantiques. `colab.ipynb` possède **7 cellules**, avec entraînement complet et validation, copie réussie du modèle sur Drive.
- Agrégats publiés du rapport de classification test : précision macro **0,74**, rappel macro **0,73**, F1 macro **0,72**, F1 pondéré **0,83** (tous arrondis par scikit-learn). Accuracy test exacte relevée séparément : **0,8448**.
- Le notebook VS Code contient encore une commande ImageMagick `!import datasets`, des commentaires historiques et un chemin absolu vers le checkpoint du Mac : éléments à corriger sans modifier ni inventer les sorties mesurées.
- Documentation `README.md` et `AVANCEMENT.md` actualisée après contrôle des deux notebooks publiés.
### 18 octobre 2026 — Publication du checkpoint entraîné dans une GitHub Release

- L'archive **`bert-ledgar.zip`** contenant le modèle BERT et le tokenizer a été publiée comme asset de la Release **[`v1.0-bert-ledgar`](https://github.com/Charles-Scannavino/Projet_Deep_Learning/releases/tag/v1.0-bert-ledgar)** du repository principal privé.
- Vérification via GitHub : Release publiée, non brouillon, asset disponible de **405 734 596 octets** (~406 Mo décimaux). Le fichier n'est pas suivi dans l'historique Git `main` afin d'éviter les limites GitHub pour les fichiers volumineux.
- Le README pointe désormais vers la Release et le téléchargement direct ; les instructions expliquent le rechargement local du checkpoint, sans réentraîner BERT.
- Les performances de classification ne sont pas modifiées par cette publication. La revue technique et la reproductibilité des notebooks restent à approfondir.

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
