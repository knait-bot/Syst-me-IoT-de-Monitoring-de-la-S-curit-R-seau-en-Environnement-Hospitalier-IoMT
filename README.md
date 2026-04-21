# IoMT Network Security Monitoring on Raspberry Pi

## Description

Ce projet présente un système IoT de monitoring de la sécurité réseau dans un environnement hospitalier IoMT.  
L’objectif est de détecter les anomalies et intrusions dans le trafic réseau des dispositifs médicaux connectés, tout en tenant compte des contraintes d’un environnement Edge basé sur Raspberry Pi.

Le système repose sur une architecture en quatre couches :

- **Sensing Layer** : collecte ou simulation des flux réseau IoMT
- **Network Layer** : transmission des données selon plusieurs stratégies
- **Data Processing Layer** : inférence par modèles de Machine Learning
- **Application Layer** : visualisation des résultats dans un dashboard local

## Objectifs du projet

- surveiller le trafic réseau IoMT
- détecter les attaques et anomalies
- comparer plusieurs modèles ML légers
- comparer plusieurs stratégies de transmission
- déployer l’inférence sur Raspberry Pi
- mesurer le temps, le CPU, la RAM et l’énergie logicielle
- visualiser les résultats dans un dashboard local

## Dataset utilisé

Le projet utilise le dataset **CICIoMT2024**, spécialisé dans le trafic réseau IoMT.

Comme le dataset complet est très volumineux, un **échantillon stratifié de 300 000 lignes** a été utilisé pour l’entraînement sur PC, afin de conserver la distribution des classes tout en réduisant le coût de calcul.

## Modèles testés

Les modèles évalués sont :

- Thresholding
- Logistic Regression
- Decision Tree

Deux configurations ont été étudiées :

- **classification binaire** : `Normal` vs `Attack`
- **classification multi-classe** : identification détaillée du type d’attaque

## Résultats principaux

- Le **Decision Tree binaire** offre le meilleur compromis pour la détection rapide `Normal/Attack`
- Le **Decision Tree multi-classe** permet une analyse plus fine du type d’attaque
- La stratégie **S2_batch** est la plus économique en énergie
- La stratégie **S3_anomaly_only** est adaptée à un système d’alerte
- La conversion **ONNX** permet de réduire la taille des modèles avant déploiement

## Optimisation Edge

Avant le déploiement sur Raspberry Pi, les modèles compatibles ont été convertis du format `joblib` vers le format `ONNX`.

### Comparaison joblib / ONNX

| Modèle | Taille joblib (KB) | Taille ONNX (KB) | Gain (%) |
|---|---:|---:|---:|
| logistic_regression_binary | 4.94 | 1.84 | 62.80 |
| decision_tree_binary | 9.76 | 3.93 | 59.76 |
| logistic_regression_multiclass | 11.59 | 5.56 | 52.06 |
| decision_tree_multiclass | 21.41 | 12.04 | 43.78 |

## Architecture du projet

```text
PC / Anaconda
 ├── préparation des données
 ├── entraînement des modèles
 ├── génération des graphes
 ├── export joblib / conversion ONNX
 └── transfert vers Raspberry Pi

Raspberry Pi
 ├── pi_sensing_replay.py
 ├── pi_network_layer.py
 ├── pi_processing_inference.py
 └── app_dashboard.py
