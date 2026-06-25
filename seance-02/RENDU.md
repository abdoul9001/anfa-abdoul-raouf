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
==================================
### Exercice 1 : QCM conceptuel   
==================================

1.1 Réponse : C — Un conteneur partage le noyau de la machine hôte.

**Justification** : contrairement à une machine virtuelle qui embarque son propre OS complet (noyau inclus), un conteneur Docker s'exécute directement sur le noyau Linux de la machine hôte grâce aux mécanismes d'isolation que sont les namespaces et les cgroups.

1.2 Réponse : B — L'image est un modèle figé en lecture seule ; le conteneur est une instance en cours d'exécution.

**Justification** : une image Docker est un artefact statique et immuable (comparable à une classe en POO), tandis qu'un conteneur est le processus vivant créé à partir de cette image (comparable à une instance de classe).

1.3 Réponse : B — Les namespaces.

**Justification** : les namespaces Linux (PID, NET, MNT, UTS, IPC, USER) permettent à chaque conteneur de disposer de sa propre vue isolée des ressources système (processus, réseau, système de fichiers, etc.), sans que les autres conteneurs ou l'hôte ne soient visibles.

1.4 Réponse : A — Les cgroups.

**Justification** : les control groups (cgroups) du noyau Linux permettent de définir et d'appliquer des quotas stricts de consommation de ressources (CPU, mémoire RAM, I/O disque) pour chaque conteneur, empêchant ainsi qu'un conteneur accapare toutes les ressources de l'hôte.

1.5 Réponse : B — Dans une machine virtuelle Linux invisible gérée par Docker Desktop.

**Justification** : macOS ne dispose pas d'un noyau Linux natif ; Docker Desktop y fait donc tourner une VM Linux légère (basée sur HyperKit ou Apple Hypervisor Framework selon la version) à l'intérieur de laquelle s'exécutent réellement les conteneurs.

1.6 Réponse : B — La société d'origine qui a créé et open-sourcé Docker en 2013.

**Justification** : DotCloud était une startup PaaS (Platform as a Service) qui, ne trouvant pas le succès commercial escompté, a décidé en 2013 de libérer en open source son outil interne de conteneurisation sous le nom "Docker", avant de pivoter et de renommer l'entreprise elle-même Docker Inc.

1.7 Réponse : C — Docker a apporté un format d'image portable, une CLI simple et un registre public, en s'appuyant sur les mêmes primitives que LXC.

**Justification** : Docker ne réinvente pas la roue au niveau noyau (namespaces et cgroups existaient déjà et étaient exploités par LXC), mais il apporte une couche d'abstraction supérieure : un format d'image en couches (layers), une interface en ligne de commande intuitive, et Docker Hub comme registre public, rendant la conteneurisation accessible à tous.

1.8 Réponse : B — Open Container Initiative — une norme ouverte pour les images et le runtime.

**Justification** : l'OCI, fondée en 2015 sous l'égide de la Linux Foundation, définit des spécifications ouvertes et standardisées pour le format des images de conteneurs (image-spec) et pour leur exécution (runtime-spec), garantissant l'interopérabilité entre les différents outils de l'écosystème (Docker, Podman, containerd, etc.).


========================================
### Exercice 2 : Lecture et analyse d'un Dockerfile   
========================================

2.1 Explication de chaque instruction

Instruction|Rôle
-|-
FROM python:3.11 | Définit l'image de base à utiliser ; ici l'image officielle Python 3.11, qui servira de fondation pour toutes les couches suivantes.
WORKDIR /application|Crée le répertoire /application s'il n'existe pas et le définit comme répertoire de travail courant pour toutes les instructions suivantes (COPY, RUN, CMD).
COPY . /application|Copie l'intégralité du contexte de build (le répertoire local depuis lequel on lance docker build) vers /application à l'intérieur de l'image.
RUN pip install -r requirements.txt|Exécute la commande d'installation des dépendances Python listées dans requirements.txt au moment du build, en créant une nouvelle couche dans l'image.
CMD ["python", "main.py"]|Définit la commande par défaut exécutée au démarrage du conteneur ; peut être surchargée à l'exécution avec docker run ... .

2.2 Différence entre EXPOSE 5000 et -p 5000:5000 de docker run

- EXPOSE 5000 est une métadonnée déclarative inscrite dans l'image : elle indique aux utilisateurs (et aux outils d'orchestration) que l'application écoute sur le port 5000, mais n'ouvre aucun port réel. C'est de la documentation.

- -p 5000:5000 dans docker run est une action d'exécution qui crée effectivement une règle de routage réseau entre le port 5000 de la machine hôte et le port 5000 du conteneur, rendant le service accessible depuis l'extérieur.

sans -p, même avec EXPOSE, le service reste inaccessible depuis l'hôte

2.3 Deux problèmes selon les bonnes pratiques

- Problème 1 : Image de base trop lourde

python:3.11 est l'image complète Debian avec l'ensemble des outils système, ce qui représente environ 900 Mo. Pour une application de production, cela augmente inutilement la surface d'attaque, le temps de build, et les coûts de transfert réseau.

Correction : utiliser python:3.11-slim (environ 130 Mo) ou python:3.11-alpine (environ 50 Mo).

- Problème 2 : Mauvais ordre des instructions — le cache n'est pas optimisé

En faisant COPY . /application avant RUN pip install -r requirements.txt, le moindre changement dans un fichier source (même un commentaire dans main.py) invalide la couche de cache et force la réinstallation complète de toutes les dépendances, même si requirements.txt n'a pas changé.

Correction : copier d'abord uniquement requirements.txt, installer les dépendances, puis copier le reste du code.

2.4 Dockerfile corrigé

    FROM python:3.11-slim

    WORKDIR /application

    COPY requirements.txt .

    RUN pip install --no-cache-dir -r requirements.txt

    COPY . .

    RUN useradd --create-home appuser

    RUN chown -R appuser:appuser /application

    USER appuser

    EXPOSE 5000

    CMD ["python", "main.py"]


========================================
### Exercice 3 : Diagnostic   
========================================

3.1 Le build qui échoue

a. Cause précise de l'erreur

L'instruction RUN pip install -r requirements.txt est exécutée avant COPY . .. Au moment où Docker exécute cette commande RUN, le fichier requirements.txt n'existe pas encore dans le système de fichiers de l'image (il n'a pas encore été copié depuis la machine hôte). D'où l'erreur No such file or directory.

b. Correction du Dockerfile

    FROM python:3.11-slim

    WORKDIR /app

    # Copier le fichier de dépendances AVANT de tenter de l'utiliser
    COPY requirements.txt .

    # Installer les dépendances maintenant que le fichier existe dans l'image
    RUN pip install -r requirements.txt

    # Copier le reste du code source
    COPY . .

    CMD ["python", "main.py"]

c. Pourquoi cette erreur illustre une mauvaise compréhension de Docker

L'étudiant a confondu le contexte de build Docker avec le système de fichiers local. Chaque instruction d'un Dockerfile s'exécute dans un contexte isolé, couche par couche, à l'intérieur de l'image en construction. La machine hôte et son système de fichiers n'existent pas pour Docker pendant un RUN : seuls les fichiers explicitement copiés via COPY ou ADD dans les étapes précédentes sont disponibles. L'ordre des instructions dans un Dockerfile est donc déterministe et fondamental.

3.2 Le conteneur qui ne voit pas l'autre

a. Erreur dans le DATABASE_URL

L'URL utilise localhost comme hôte de la base de données :

    DATABASE_URL: "postgresql://user:password@localhost:5432/anfa"

Dans un environnement Docker Compose, localhost fait référence au conteneur lui-même (le loopback de api), et non au conteneur db. Chaque conteneur a son propre espace réseau isolé, donc localhost dans api pointe vers api, pas vers db.

b. Correction

Il faut remplacer localhost par le nom du service tel que défini dans le docker-compose.yml. Docker Compose crée automatiquement un réseau interne et enregistre chaque service sous son nom comme entrée DNS.

    yamlenvironment:
        DATABASE_URL: "postgresql://user:password@db:5432/anfa"


========================================
### Exercice 4 : Optimisation d'image   
========================================

Le Dockerfile problématique :

    dockerfile
    FROM ubuntu:22.04
    RUN apt-get update
    RUN apt-get install -y python3 python3-pip
    RUN apt-get install -y curl wget git build-essential
    COPY . /app
    WORKDIR /app
    RUN pip3 install -r requirements.txt
    CMD ["python3", "downloader.py"]

Quatre problèmes identifiés

Problème 1 : Image de base inadaptée (ubuntu:22.04)

Utiliser une image Ubuntu complète pour une application Python embarque des centaines de paquets inutiles (init systems, outils GNU, bibliothèques système...), contribuant largement aux 1,1 Go constatés. L'image python:3.11-slim ou python:3.11-alpine suffit amplement et réduit la taille de 80 à 95%.

Problème 2 : Multiplication des instructions RUN — mauvaise gestion du cache et des couches

Chaque RUN crée une nouvelle couche dans l'image. Avoir apt-get update dans une couche séparée de apt-get install est particulièrement dangereux : Docker peut mettre en cache la couche update et utiliser un index de paquets périmé lors d'un rebuild ultérieur, causant des installations incohérentes. Ces instructions doivent être fusionnées en un seul RUN.

Problème 3 : Paquets inutiles installés (curl, wget, git, build-essential)

L'application n'a besoin que de requests (d'après le requirements.txt). Installer git, build-essential et des outils de téléchargement augmente inutilement la surface d'attaque (plus de binaires = plus de vecteurs de compromission potentiels) et alourdit considérablement l'image.

Problème 4 : Absence de nettoyage du cache apt et aucun utilisateur non-root

Après apt-get install, le cache des paquets reste dans l'image (/var/cache/apt/), ajoutant des dizaines de Mo inutiles. Il faut systématiquement terminer par && rm -rf /var/lib/apt/lists/*. De plus, l'application s'exécute en tant que root, ce qui représente un risque de sécurité majeur.

Dockerfile corrigé

    dockerfile
    
    # Image de base Python officielle slim : ~130 Mo au lieu de ~900 Mo pour ubuntu
    FROM python:3.11-slim

    # Créer un utilisateur non-root dès le début
    RUN useradd --create-home appuser

    WORKDIR /app

    # Copier uniquement requirements.txt pour optimiser le cache Docker :
    # si requirements.txt ne change pas, pip install est réutilisé depuis le cache
    COPY requirements.txt .

    # Installer les dépendances Python sans cache pip pour réduire la taille de l'image
    RUN pip install --no-cache-dir -r requirements.txt

    # Copier le reste du code source
    COPY . .

    # Donner les droits sur le répertoire à l'utilisateur applicatif
    RUN chown -R appuser:appuser /app

    # Basculer sur l'utilisateur non-root
    USER appuser

    CMD ["python", "downloader.py"]


========================================
### Exercice 5 : Mini-cas d'architecture   
========================================

a. Services à conteneuriser


Service|Rôle
-|-
pipeline|Conteneur Python qui s'exécute chaque nuit : récupère le fichier JSON Lines depuis le FTP, nettoie et agrège les données GPS, puis écrit les résultats dans MinIO.
minio|Serveur de stockage objet compatible S3 qui joue le rôle de lac de données : stocke les fichiers bruts et les résultats agrégés, accessibles par le pipeline et Jupyter.
jupyter|Serveur de notebooks Jupyter permettant à Kossi et Awa d'explorer les données agrégées stockées dans MinIO et de produire des graphiques analytiques.

b. Restart policy pour le script pipeline

Recommandation : on-failure

Le script pipeline est conçu pour s'exécuter ponctuellement (batch nocturne), pas pour tourner en permanence. La politique on-failure permet de le relancer automatiquement s'il échoue pour une raison transitoire (FTP temporairement indisponible, erreur réseau), mais sans le relancer indéfiniment une fois qu'il s'est terminé avec succès (code de sortie 0). Utiliser always ou unless-stopped serait incorrect car Docker relancerait le script même après une exécution réussie, créant une boucle infinie de traitements inutiles.


c. Passer la date au script pour rejouer le pipeline

Mécanisme 1 : Variable d'environnement via docker run ou docker-compose run

    # Rejouer le pipeline pour la date du 2024-01-15
    docker compose run -e PIPELINE_DATE=2024-01-15 pipeline

Le script Python récupère ensuite la date avec 

    os.environ.get("PIPELINE_DATE", date.today().isoformat()).

Mécanisme 2 : Argument de commande via la directive command ou en surcharge à l'exécution

    # Surcharger le CMD défini dans le Dockerfile
    docker compose run pipeline python main.py --date 2024-01-15

Recommandation : variable d'environnement (Mécanisme 1)

C'est l'approche préférable car elle ne nécessite pas de modifier la définition du CMD dans le Dockerfile, s'intègre naturellement dans les systèmes d'orchestration (Airflow, cron, etc.) et constitue la convention standard des conteneurs (les 12-factor apps préconisent la configuration par variables d'environnement).


d. Pourquoi ne pas mettre le script dans le conteneur Jupyter ?

Mélanger le script pipeline dans le conteneur Jupyter violerait le principe de responsabilité unique : Jupyter est un serveur interactif conçu pour rester actif en permanence, tandis que le pipeline est un batch conçu pour s'exécuter et se terminer. Les mettre ensemble crée plusieurs problèmes : on ne peut plus gérer leurs cycles de vie indépendamment (redémarrer Jupyter pour corriger un bug du pipeline, ou vice-versa), leurs dépendances Python peuvent entrer en conflit, et le conteneur Jupyter hériterait de tous les droits et accès FTP du pipeline, augmentant inutilement la surface d'attaque. L'isolation en conteneurs séparés offre plus de sécurité, de maintenabilité et de flexibilité pour le scheduling.


e. Squelette de docker-compose.yml

    version: "3.8"

    services:

    # Serveur de stockage objet MinIO (compatible S3)
    # Démarre en premier car les autres services en dépendent
    minio:
        image: minio/minio:latest
        # Commande pour démarrer MinIO en mode serveur
        command: server /data --console-address ":9001"
        environment:
        MINIO_ROOT_USER: admin
        MINIO_ROOT_PASSWORD: password123
        ports:
        # Port API S3
        - "9000:9000"
        # Port console web MinIO
        - "9001:9001"
        volumes:
        # Volume persistant pour ne pas perdre les données au redémarrage
        - minio_data:/data
        # Toujours redémarrer MinIO, c'est un service permanent
        restart: unless-stopped

    # Script Python de traitement nocturne des données GPS
    pipeline:
        build: ./pipeline
        environment:
        # URL du serveur FTP externe (à adapter)
        FTP_HOST: ftp.anfa-data.example.com
        # URL MinIO interne (utilise le nom de service Docker comme hôte)
        MINIO_ENDPOINT: http://minio:9000
        MINIO_ACCESS_KEY: admin
        MINIO_SECRET_KEY: password123
        MINIO_BUCKET: gps-data
        # Date de traitement, par défaut aujourd'hui ; peut être surchargée
        # avec : docker compose run -e PIPELINE_DATE=2024-01-15 pipeline
        PIPELINE_DATE: ""
        # Ne relancer qu'en cas d'échec (code sortie != 0)
        # Évite la boucle infinie après une exécution réussie
        restart: on-failure
        # Attendre que MinIO soit démarré avant de lancer le pipeline
        depends_on:
        - minio

    # Serveur Jupyter pour l'exploration des données dans MinIO
    jupyter:
        image: jupyter/scipy-notebook:latest
        environment:
        # URL MinIO pour accès depuis les notebooks
        MINIO_ENDPOINT: http://minio:9000
        MINIO_ACCESS_KEY: admin
        MINIO_SECRET_KEY: password123
        ports:
        # Port d'accès à l'interface Jupyter
        - "8888:8888"
        volumes:
        # Persister les notebooks localement pour ne pas les perdre
        - ./notebooks:/home/jovyan/work
        # Service permanent, toujours relancer sauf arrêt manuel
        restart: unless-stopped
        depends_on:
        - minio

    # Volumes nommés pour la persistance des données
    volumes:
    minio_data:
## Difficultés rencontrées
Aucune 