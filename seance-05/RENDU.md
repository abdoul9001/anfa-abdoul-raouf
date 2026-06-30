# Rendu  Séance 5
**Nom et prénom :** SONHOUIN Abdoul-raouf
## Résumé de la séance
Déploiement d'un cluster Spark standalone (1 master + 2 workers) via Docker Compose, exécution de jobs PySpark distribués lisant et écrivant dans MinIO (référentiel Anfa et historique de trajets simulé), avec une comparaison entre exécution locale et exécution sur cluster. Le bonus Spark sur Kubernetes (via Kind + Spark Operator) a également été réalisé.
## Étapes principales
1. Déploiement du cluster Spark standalone (1 master + 2 workers) via Docker Compose.
2. Préparation de MinIO et upload du référentiel.
3. Premier job distribué (`analyse_referentiel_cluster.py`) : statistiques de base.
4. Génération d'un historique simulé de trajets et job d'analyse des heures de pointe.
5. Comparaison subjective entre mode local et mode cluster.
## Captures d'écran
### Dashboard Spark Master avec 2 workers
![Spark Master Dashboard](captures/spark-master-dashboard.png)
### Application Spark exécutée avec succès
![Application terminée](captures/spark-app-completed.png)
### Résultats du Top 10 dans la console
![Top 10 heures de pointe](captures/top10-heures-pointe.png)
### Bucket anfa-processed avec heures_de_pointe partitionné
![MinIO heures_de_pointe](captures/minio-heures-pointe.png)

## Réflexion : local vs cluster
<Vos observations subjectives : durée perçue, expérience, dans quel cas vous utiliseriez l'un ou l'autre.>
## Bonus Spark sur Kubernetes
Oui
## Réponses aux exercices d'application
<À compléter d'après les énoncés fournis avec l'assignment.>
## Difficultés rencontrées
<Aucune | Décrivez brièvement.