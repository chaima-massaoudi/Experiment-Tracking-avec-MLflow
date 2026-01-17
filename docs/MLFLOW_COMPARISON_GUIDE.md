# Guide Complet : Comparaison des Runs dans MLflow UI

## Étape 7 : Comparaison dans l'UI MLflow

Ce guide vous montre comment comparer plusieurs runs d'expériences YOLO dans l'interface MLflow pour analyser les métriques et les artefacts.

---

## 🎯 Objectif

Comparer les différentes configurations YOLO (époques, taille d'image, learning rate) pour identifier la meilleure configuration basée sur :
- **mAP@50** : Mean Average Precision à IoU 0.5
- **mAP50-95** : Mean Average Precision moyennée sur IoU 0.5 à 0.95
- **Precision** : Précision du modèle
- **Recall** : Rappel du modèle

---

## 📋 Étapes de Comparaison

### 1. Accéder à l'Expérience

1. Ouvrez votre navigateur et allez sur **http://localhost:5000**
2. Dans la barre latérale gauche, cliquez sur l'expérience **cv_yolo_tiny** (ou Experiment ID 1)
3. Vous verrez la liste des 9 runs générés par le script de grid search

### 2. Sélectionner les Runs à Comparer

**Méthode manuelle** (recommandée) :

1. **Cliquez sur les checkboxes** à gauche de chaque run que vous souhaitez comparer
   - Sélectionnez au moins 2 runs, maximum 10 pour une comparaison claire
   - Exemple : Sélectionnez 3-4 runs avec différentes configurations

2. Une fois les runs sélectionnés :
   - Un bouton **"Compare"** apparaît en haut de la table des runs
   - Le nombre de runs sélectionnés est affiché

### 3. Cliquer sur "Compare"

1. Cliquez sur le bouton **"Compare"** (généralement en haut à gauche de la table)
2. Attendez que la page de comparaison se charge
3. Vous serez redirigé vers : `http://localhost:5000/#/experiments/1/compare-runs/...`

### 4. Analyser la Page de Comparaison

La page de comparaison comporte plusieurs sections :

#### A. **Vue d'ensemble (Overview)**
- Tableau montrant tous les runs sélectionnés côte à côte
- Informations de base : Run Name, Duration, Start Time

#### B. **Paramètres (Parameters)**
- Comparez les hyperparamètres entre les runs :
  - `epochs` : Nombre d'époques d'entraînement (3, 5, 10)
  - `imgsz` : Taille d'image (320, 416, 640)
  - `lr0` : Learning rate initial (0.001, 0.01, 0.1)
  - `seed` : Seed aléatoire (42)
  - `model` : Architecture (yolov8n.pt)

#### C. **Métriques (Metrics)**
Cliquez sur l'onglet **"Metrics"** pour voir :

| Métrique | Description | Objectif |
|----------|-------------|----------|
| **metrics/precision(B)** | Précision des boîtes de détection | ⬆️ Plus élevé = Meilleur |
| **metrics/recall(B)** | Rappel des boîtes de détection | ⬆️ Plus élevé = Meilleur |
| **metrics/mAP50(B)** | mAP à IoU 0.5 | ⬆️ Plus élevé = Meilleur |
| **metrics/mAP50-95(B)** | mAP moyenné IoU 0.5-0.95 | ⬆️ Plus élevé = Meilleur |

**Astuce** : Vous pouvez :
- Trier les colonnes pour identifier le meilleur run
- Cliquer sur les en-têtes pour changer l'ordre de tri
- Sélectionner des métriques spécifiques à afficher

#### D. **Graphiques de Métriques**
- Graphiques parallèles montrant l'évolution des métriques
- Possibilité de superposer les courbes pour comparer visuellement
- Utilisez la légende pour identifier chaque run

### 5. Explorer les Artefacts

Pour chaque run, vous pouvez consulter les artefacts générés :

1. **Retournez à la liste des runs** (cliquez sur "cv_yolo_tiny" dans la sidebar)
2. **Cliquez sur un nom de run** spécifique pour ouvrir sa page de détails
3. Faites défiler jusqu'à la section **"Artifacts"**

#### Artefacts disponibles :

| Fichier | Description |
|---------|-------------|
| **results.png** | Graphiques d'entraînement (loss, mAP, precision, recall par époque) |
| **confusion_matrix.png** | Matrice de confusion des prédictions |
| **confusion_matrix_normalized.png** | Matrice de confusion normalisée |
| **F1_curve.png** | Courbe F1-score |
| **P_curve.png** | Courbe Precision-Confidence |
| **PR_curve.png** | Courbe Precision-Recall |
| **R_curve.png** | Courbe Recall-Confidence |
| **weights/best.pt** | Poids du meilleur modèle (téléchargeable) |
| **weights/last.pt** | Poids du dernier checkpoint |

**Pour télécharger un artefact** :
1. Cliquez sur le nom du fichier dans la section Artifacts
2. Le fichier s'ouvre dans le visualiseur MLflow (pour les images)
3. Utilisez le bouton de téléchargement pour sauvegarder localement

---

## 🔍 Analyse Recommandée

### 1. Identifier le Meilleur Run

Après avoir comparé les runs, identifiez celui avec :
- ✅ **mAP50-95 le plus élevé** (métrique principale pour YOLO)
- ✅ **Bon équilibre Precision/Recall**
- ✅ **Temps d'entraînement acceptable**

Exemple de critères :
```
Meilleur Run :
- mAP50-95 > 0.25
- mAP50 > 0.30
- Precision > 0.40
- Recall > 0.70
```

### 2. Analyser l'Impact des Hyperparamètres

Comparez les runs pour comprendre l'impact de chaque paramètre :

#### **Impact des Époques** (epochs: 3, 5, 10)
- Comparez des runs avec **même imgsz et lr0** mais différentes époques
- Observer si plus d'époques = meilleur mAP (attention à l'overfitting)

#### **Impact de la Taille d'Image** (imgsz: 320, 416, 640)
- Comparez des runs avec **même epochs et lr0** mais différentes tailles
- Images plus grandes = généralement meilleur mAP mais temps plus long

#### **Impact du Learning Rate** (lr0: 0.001, 0.01, 0.1)
- Comparez des runs avec **même epochs et imgsz** mais différents lr0
- Trouver le lr optimal (ni trop faible, ni trop élevé)

### 3. Examiner les Courbes d'Entraînement

Pour chaque run prometteur :

1. Ouvrez **results.png** :
   - Vérifiez que les loss diminuent régulièrement
   - Assurez-vous que val/loss ne diverge pas de train/loss (overfitting)
   - Observez la convergence des métriques

2. Ouvrez **confusion_matrix.png** :
   - Vérifiez la détection correcte de la classe "person"
   - Identifiez les faux positifs/négatifs

3. Ouvrez **PR_curve.png** :
   - Courbe Precision-Recall doit tendre vers le coin supérieur droit
   - Surface sous la courbe = mAP

---

## 📊 Interface MLflow : Navigation Rapide

### URLs Utiles

| Page | URL |
|------|-----|
| **Accueil MLflow** | http://localhost:5000 |
| **Expérience cv_yolo_tiny** | http://localhost:5000/#/experiments/1 |
| **Comparaison de runs** | Sélectionner runs → Bouton "Compare" |

### Raccourcis Clavier (dans l'UI)

| Action | Raccourci |
|--------|-----------|
| Rechercher | `/` |
| Sélectionner run | Clic sur checkbox |
| Ouvrir run | Clic sur nom du run |

---

## 🎓 Exemple Pratique : Comparaison de 3 Runs

### Configuration Exemple

Supposons que vous avez sélectionné ces 3 runs :

| Run | Epochs | ImgSz | LR | mAP50 | mAP50-95 | Precision | Recall |
|-----|--------|-------|-----|-------|----------|-----------|--------|
| **Run 1** | 3 | 320 | 0.01 | 0.32 | 0.27 | 0.45 | 0.77 |
| **Run 2** | 5 | 416 | 0.01 | 0.38 | 0.31 | 0.52 | 0.80 |
| **Run 3** | 10 | 640 | 0.001 | 0.41 | 0.34 | 0.58 | 0.82 |

### Analyse

1. **Meilleur Run : Run 3**
   - ✅ mAP50-95 le plus élevé (0.34)
   - ✅ Meilleure précision (0.58)
   - ✅ Meilleur recall (0.82)
   - ⚠️ Temps d'entraînement plus long (10 époques, 640px)

2. **Compromis Vitesse/Performance : Run 2**
   - ✅ Bonnes performances (mAP50-95 = 0.31)
   - ✅ Temps d'entraînement modéré (5 époques, 416px)
   - ✅ Bon équilibre pour production

3. **Run Rapide : Run 1**
   - ✅ Entraînement rapide (3 époques, 320px)
   - ⚠️ Performances plus faibles
   - 💡 Idéal pour prototypage rapide

---

## 💡 Conseils Pratiques

### ✅ Bonnes Pratiques

1. **Comparez max 5 runs à la fois** pour une lisibilité optimale
2. **Utilisez les filtres** pour sélectionner des runs similaires
3. **Notez vos observations** directement dans les tags MLflow
4. **Téléchargez les artefacts** des meilleurs modèles pour référence

### ⚠️ Points d'Attention

1. **Seed** : Tous les runs utilisent seed=42, donc reproductibles
2. **Dataset** : Tiny COCO (60 images) = résultats à prendre avec précaution
3. **Overfitting** : Surveillez val/loss vs train/loss dans results.png
4. **Temps** : Runs avec imgsz=640 et epochs=10 sont beaucoup plus lents

---

## 🛠️ Dépannage

### Problème : Bouton "Compare" n'apparaît pas
- **Solution** : Assurez-vous d'avoir sélectionné au moins 2 runs
- Vérifiez que les checkboxes sont bien cochées

### Problème : Métriques manquantes
- **Solution** : Vérifiez que le run s'est terminé avec succès
- Consultez les logs du run pour identifier les erreurs

### Problème : Artefacts non visibles
- **Solution** : Attendez la fin complète du run
- Vérifiez le dossier `runs/` localement si nécessaire

---

## 📖 Ressources

- [Documentation MLflow - Comparing Runs](https://mlflow.org/docs/latest/tracking.html#comparing-runs)
- [Documentation Ultralytics - Metrics](https://docs.ultralytics.com/guides/yolo-performance-metrics/)
- [mAP Explained](https://jonathan-hui.medium.com/map-mean-average-precision-for-object-detection-45c121a31173)

---

**🎉 Vous savez maintenant comment analyser et comparer vos expériences YOLO dans MLflow !**

Pour toute question, consultez votre instructeur ou la documentation MLflow.
