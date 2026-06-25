# TD de Groupe S2 — Architecture Big Data

Conception de l'architecture Big Data d'un opérateur télécom fictif guinéen (TelGui SA).

- Cours : Data Mining & KDD — Séance 2 (Master 1)
- Université Kofi Annan de Guinée — Faculté des Sciences Informatiques
- Travail Dirigé de Groupe — Noté /2
- Groupe : G1 (à ajuster : 3-4 membres réels)
- Outil schéma : Lucidchart ou draw.io
- Deadline : Mercredi 29 avril 23h59

## 1. Contexte

TelGui SA est un opérateur de téléphonie mobile fictif basé en Guinée (~4 millions d'abonnés). Il génère d'énormes volumes de données (appels, Mobile Money, réseau, SAV), à grande vitesse et dans des formats variés. L'architecture doit collecter, stocker, traiter et exploiter ces données pour prédire le churn et détecter la fraude Mobile Money en temps réel.

## 2. Schéma de l'architecture

Voir le fichier `architecture_telgui.png`. L'architecture comporte 6 couches enchaînées :

```
L1 Sources  ->  L2 Ingestion  ->  L3 Stockage  ->  L4 Traitement  ->  L5 Modèles  ->  L6 Présentation
```

## 3. Détail des 6 couches

| Couche | Contenu / outil | Justification |
|---|---|---|
| 1. Sources de données | CDR/logs d'appels (CSV), Mobile Money (JSON), Tickets SAV (texte/JSON), Données réseau (logs), CRM (SQL) | Au moins 4 sources hétérogènes avec des formats différents -> architecture Big Data justifiée. |
| 2. Ingestion | Apache Kafka | Absorbe des flux temps réel à grand débit, découple sources et traitement, aucune donnée perdue même en cas de pic ou de panne. |
| 3. Stockage | Data Lake (HDFS/S3) + Data Warehouse (BigQuery/PostgreSQL) | Lake = brut, tous formats, faible coût, ML. Warehouse = structuré (schéma en étoile), requêtes rapides, BI. |
| 4. Traitement | Batch : Spark (nuit). Temps réel : Spark Streaming + Kafka | Batch = gros volumes en différé (précis). Temps réel = faible latence (fraude en ~50 ms). |
| 5. Modèles et résultats | XGBoost (churn), Isolation Forest (fraude), Feature Store | Le Feature Store précalcule des variables réutilisées à l'entraînement et en production (cohérence). |
| 6. Présentation | Power BI (direction), API FastAPI (conseillers), SMS auto (clients), Notebooks (data scientists) | Chaque utilisateur accède à l'outil adapté à son besoin. |

## 4. Points clés

- Composant le plus critique : Kafka (ingestion) — sans données entrantes, rien ne fonctionne.
- Data Lake (brut, exploration) vs Data Warehouse (structuré, BI/reporting).
- Contraintes réelles en Guinée : coût cloud, connectivité, compétences, réglementation BCRG.
- Version légère possible : PostgreSQL + Python + Scikit-learn + Streamlit.
