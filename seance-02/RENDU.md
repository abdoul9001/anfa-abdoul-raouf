# Rendu - Séance 2
**Nom et prénom :** SONHOUIN Abdoul-raouf

**Identifiant GitHub :** abdoul9001

**Date de soumission :** 22/06/2026
## Résumé de la séance
J'ai écrit le Dockerfile pour conteneuriser le script analyse_referentiel.py (PySpark), construit l'image anfa-analyse:v1 avec docker build, puis orchestré un stack Compose à 3 services (MinIO, Jupyter, image custom). J'ai ensuite exploré le bucket anfa-raw depuis un notebook Jupyter via boto3 et pandas.

## Étapes principales
1. Écriture du Dockerfile et construction de l'image `anfa-analyse:v1` (taille observée : 1.17 Go).
2. Mise en place du `.dockerignore` et observation du cache de Docker.
3. Écriture du `docker-compose.yml` orchestrant MinIO, Jupyter, et l'image custom.
4. Création du notebook `exploration_minio.ipynb` qui lit les données depuis MinIO via boto3 et pandas.

## Captures d'écran
### docker compose ps
![docker compose ps](/seance-02/captures/docker-ps.png)
### Notebook Jupyter
![Notebook Jupyter](/seance-02/captures/jupyter-pandas%201.png)
![Notebook Jupyter](/seance-02/captures/jupyter-pandas%202.png)
![Notebook Jupyter](/seance-02/captures/jupyter-pandas%203.png)
![Notebook Jupyter](/seance-02/captures/jupyter-pandas%204.png)
![Notebook Jupyter](/seance-02/captures/jupyter-pandas%205.png)
## Bonus multi-stage (optionnel)
Image v1 : **1.17 GB**

Image v2-multistage : **1.17 GB**

Gain : 0 % — aucune réduction observée.

Explication : le poids de l'image vient principalement du JRE (Java) et de PySpark,
qui sont nécessaires dans l'étage final également. Le multi-stage build est efficace
pour éliminer des outils de compilation lourds qu'on n'utilise pas ici, donc le gain
est nul dans ce cas précis (cohérent avec la remarque du TP sur les limites du
multi-stage pour les apps dépendant de Java/PySpark).

## Réponses aux exercices d'application
<À compléter d'après les énoncés fournis avec l'assignment.>
## Difficultés rencontrées
Aucune 