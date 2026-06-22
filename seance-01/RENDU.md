# Rendu Séance 1
**Nom et prénom :** SONHOUIN Abdoul-raouf

## Résumé de la séance
- Installation et verification de l'environnement
- installation de docker selon notre OS
- Creation de compte Github pour ce qui ne l'on pas
- Forker le dépôt : https://github.com/denisakp/cloud-bigdata-anfa-resources
- on n'a creer notre fork puis apres on la cloner en local puis creer la branche et le dossier seance-01
- Télécharger l'image MinIO et  Lancer MinIO via un commande longue puis on n'a verfier si elle tourne
- on n'a pris aussi que Administrer MinIO ce fait via les lignes de commande avec **mc**
        1- Pour entrer dans le conteneur MinIO : **docker exec -it anfa-minio sh**
        2- on la configurer 
        3- on n'a Créer le bucket anfa-raw
        4- Créer une paire de clés applicatives pour le script Python
- On n'a Préparer l'environnement Python dans seance-01
- Créer le fichier requirements.txt
- Écrire le script upload_referentiel.py
- la commande docker longue, peu lisible et difficile a versionner on l'a rendu plus simple en creer un fichier docker-compose.yaml qui decrit la configuration et pour le lance on utiliser un commande plus courte **docker compose up -d**



## Étapes principales
-  Installation et vérification de l'environnement
-  Forker le dépôt du cours et préparer votre branche de travail
-  Récupérer l'image MinIO et la lancer
-  Administrer MinIO en ligne de commande avec mc
-  Déposer le référentiel d'Anfa via Python
-  Aperçu : la même chose avec docker-compose.yml

## Capture d'écran
![alt text](/seance-01/captures/bucket-anfa-raw.png)

## Difficultés rencontrées
docker run -d \--name anfa-minio \-p 9000:9000 \-p 9001:9001 \-v anfa-minio-data:/data \-e MINIO_ROOT_USER=anfa-admin \-e MINIO_ROOT_PASSWORD=anfa-password-2026 \
minio/minio server /data --console-address ":9001"

En executant cette commande j'ai eu un erreur qui disait \ est compris pas les mac et linux et alors j'ai reformater la commande pour quel puis marcher sur windows

## Exercices d'application
**********************************
-Exercice 1 : QCM conceptuel     |
**********************************

**1.1 = D. Open source obligatoire** 
**justifcation**: les 5 caractéristiques sont : libre-service à la demande, accès réseau large, mutualisation des ressources (pooling), élasticité rapide, et service mesuré.

**1.2 = C. SaaS**
**justifcation**:Gmail est une application complète prête à l'emploi via le navigateur, sans gestion d'infrastructure ni de plateforme par l'utilisateur.

**1.3 = D. FaaS**
**justifcation**:Le besoin est une exécution événementielle, courte et sans gestion de serveur permanent.

**1.4 = C. Cloud hybride**
**justifcation**:Cela permet de garder les données sensibles on-premise/cloud privé (conformité réglementaire) tout en exploitant l'élasticité du cloud public pour les traitements non sensibles.

**1.5 = B. La situation où une entreprise ne peut plus changer de fournisseur sans coûts ou risques majeurs**
**justifcation**:C'est la définition du vendor lock-in : dépendance technique/contractuelle qui rend la migration coûteuse ou risquée

**1.3 = C. FaaS**
**justifcation**:C'est faux : la performance dépend de l'implémentation et du contexte, pas du caractère open source ou non du logiciel.

==============================================================================================================
*****************************************
Exercice 2 : Classification de services |
*****************************************
| Service               | Modèle | Justification |
|-----------------------|--------|---------------|
| Google Compute Engine | IaaS   | Fournit une VM brute ; l'utilisateur gère l'OS, le runtime et les applications. |
| AWS Lambda            | FaaS   | Exécute du code à la demande, déclenché par événement, facturé à la milliseconde, sans serveur géré par l'utilisateur. |
| Snowflake             | SaaS   | Entrepôt de données utilisable directement via une interface, aucune infrastructure à gérer. |
| Heroku                | PaaS   | Plateforme prête à recevoir une application ; runtime et scaling gérés par le fournisseur. |
| Microsoft 365         | SaaS   | Application complète (Word, Excel) accessible en ligne, sans installation. |
| Databricks            | PaaS   | Plateforme Spark managée ; l'utilisateur écrit le code, la plateforme gère l'infrastructure, le scaling et la maintenance. |
| Azure Functions       | FaaS   | Fonctions événementielles facturées à la milliseconde, pas de serveur visible. |
| Tableau Online        | SaaS   | Outil de visualisation complet accessible en ligne, sans installation locale. |



================================================================================================================
***************************************
Exercice 3 : Lecture et interprétation|
***************************************
# 3.1 Commande docker run

docker run -d --name analyse-anfa -p 8888:8888 -v /home/koffi/notebooks:/notebooks \
  -e JUPYTER_TOKEN=anfa-token \
  jupyter/pyspark-notebook

**-d** :lance le conteneur en mode détaché (arrière-plan).
**--name analyse-anfa** : donne un nom explicite au conteneur pour le retrouver facilement.
**-p 8888:8888** : mappe le port 8888 de la machine hôte vers le port 8888 du conteneur
**-v /home/koffi/notebooks:/notebooks** : monte le dossier hôte dans le conteneur, les notebooks créés sont donc persistés sur le disque local même si le conteneur est supprimé.
**-e JUPYTER_TOKEN=anfa-token** : définit une variable d'environnement, ici le token d'authentification requis pour accéder au notebook.
**jupyter/pyspark-notebook** : image Docker utilisée, contenant Jupyter avec PySpark préinstallé.

**Explication**:cette commande lance en arrière-plan un conteneur Jupyter/PySpark nommé analyse-anfa, accessible sur http://localhost:8888 et protégé par un token. Les notebooks créés sont sauvegardés de façon persistante sur la machine hôte.


# 3.2 Lecture du docker-compose.yml
a- URLs d'accès depuis le navigateur
    . API S3 : http://localhost:9000
    . Console web d'administration : http://localhost:9001

b- Non les données ne sont pas perdue. Les données sont stockées dans le volume nommé minio-data, déclaré séparément du conteneur.
   Docker rm supprime uniquement le conteneur, pas le volume. Au redémarrage, le nouveau conteneur remonte le même volume minio-data et retrouve donc l'intégralité des données précédemment écrites.

c- Problème de sécurité à corriger pour la production Les identifiants root (MINIO_ROOT_USER / MINIO_ROOT_PASSWORD) sont écrits en clair dans le fichier, avec un mot de passe faible (secret). En production, ces secrets ne doivent jamais être committés ; il faut utiliser un mécanisme de gestion de secrets (Docker secrets, variables d'environnement externes, vault) et un mot de passe fort.


==================================================================================================================================
*************************
Exercice 4 : Diagnostic |
*************************

a- Cause précise de l'erreur Le script utilise les identifiants anfa-admin / anfa-password-2026, qui ne correspondent pas à une clé d'accès API valide. La clé applicative réellement créée pour l'usage programmatique est anfa-app-key / anfa-app-secret-2026. MinIO ne reconnaît donc pas l'access_key_id fourni, d'où InvalidAccessKeyId.

b- Correction Remplacer les identifiants dans le code par la clé applicative correcte (voir code ci-dessus : anfa-app-key / anfa-app-secret-2026).

c- Pourquoi le compte root fonctionne pour la console mais pas pour l'API ici
La console web authentifie l'administrateur via le compte root MinIO, prévu pour la gestion globale du serveur (création de buckets, d'utilisateurs, de politiques). L'API S3, elle, attend des clés d'accès applicatives dédiées, créées explicitement (ici via mc) et associées à des permissions précises sur des buckets précis. Utiliser le compte root pour l'accès programmatique n'est pas la pratique recommandée ; il faut créer des clés applicatives à portée limitée, ce qui explique que seules anfa-app-key / anfa-app-secret-2026 fonctionnent pour l'upload.


==========================================================================================================================================
**************************************
Exercice 5 : Mini-cas d'architecture |
**************************************

a.  Deux limites de l'architecture actuelle
        - Absence de temps réel : un export CSV mensuel ne permet pas de produire des prédictions horaires, le délai entre la donnée et la prédiction est bien trop long.
        - Absence de scalabilité et point unique de défaillance (SPOF) : tout repose sur le PC d'une seule personne (Toyi) ; pas d'accès partagé, pas de capacité supplémentaire disponible lors des pics, et une panne de ce PC interrompt tout le processus.

b.  Besoins de la direction → caractéristique NIST
        - Prédictions quasi temps réel chaque heure → Libre-service à la demande : le pipeline peut être déclenché automatiquement à chaque cycle horaire sans intervention humaine du fournisseur.
        - Tableau de bord partagé sans installation → Accès réseau large : accessible depuis n'importe quel terminal via une simple URL.
        - Augmenter la capacité lors des pics → Élasticité rapide : scale out automatique le vendredi soir ou pendant les fêtes, puis scale in ensuite.
        - Maîtriser les coûts → Service mesuré (pay-as-you-go) : facturation à l'usage réel, sans payer pour une capacité surdimensionnée en permanence.
        - Conserver les données clients dans un environnement contrôlé → ce besoin n'est pas couvert par une caractéristique NIST ; il relève du choix du modèle de déploiement.

c.  Modèle de service par composant

        (i) Tableau de bord partagé → SaaS : outil de visualisation prêt à l'emploi (type Tableau Online, Metabase), aucune infrastructure à gérer.

        (ii) Calcul des prédictions à l'heure → PaaS (ou FaaS si chaque exécution est courte et déclenchée par un événement) : une plateforme managée type Databricks exécute le pipeline ML sans gestion de serveur par la PME.

        (iii) Stockage des données clients → IaaS / stockage objet géré en interne : pour garder le contrôle maximal requis par la conformité, un stockage objet hébergé en environnement privé est préférable à un service SaaS tiers.

d.  Modèle de déploiement recommandé
Cloud hybride : les données clients sensibles restent dans un cloud privé pour répondre à la contrainte de conformité, tandis que les calculs de prédiction et le tableau de bord sont déployés en cloud public afin de bénéficier de l'élasticité lors des pics et d'une simplicité d'accès partagé.

e.  Trois stratégies anti-vendor lock-in
        1-  Conteneuriser les traitements avec Docker, pour qu'ils tournent à l'identique sur n'importe quel fournisseur.
        2-  Décrire l'infrastructure en code avec Terraform, pour pouvoir changer de fournisseur en modifiant peu de lignes.
        3-  Privilégier les standards open source plutôt que des services propriétaires fermés.