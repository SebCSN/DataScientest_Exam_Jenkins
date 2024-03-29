# DATASCIENTEST JENKINS EXAM
# python-microservice-fastapi
Learn to build your own microservice using Python and FastAPI

## How to run??
 - Make sure you have installed `docker` and `docker-compose`
 - Run `docker-compose up -d`
 - Head over to http://localhost:8080/api/v1/movies/docs for movie service docs 
   and http://localhost:8080/api/v1/casts/docs for cast service docs

## Etape n°1 :
 - Modification du docker compose pour éviter le conflit de port entre Nginx et Jenkins.
 - Modification des Dockerfile en définissant la commande par défaut à exécuter lorsque le conteneur est lancé.
 - 1ers tests pour la construction des images Docker et pouur pousser sur le Docker Hub.

## Etape n°2 :
 - Ajout d'une étape pour arreter les services lancés par le docker compose qui peuvent être accessible aux adresses :
	- <ip_machine>:8001/api/v1/movies/docs
	- <ip_machine>:8002/api/v1/casts/docs
 - Permet de tester aussi le push enclenche Jenkins

## Etape n°3 : 
 - Sur le serveur, création du namespace dev à l'aide de la commande `kubectl create namespace dev`
 - Test de déploiement des services sur le namespace dev en utilisant un ingress qui basique (sans utiliser les variables contenues dans values)
 - Création sur cloudns.net d'un domaine dev.exam-jenkins.seb-coasne.cloudns.biz lié au serveur sur lequel est hébergé mon cluster
 - Les services devoient être accessibles aux adresses suivantes :
	- http://dev.exam-jenkins.seb-coasne.cloudns.biz/api/v1/casts/docs
	- http://dev.exam-jenkins.seb-coasne.cloudns.biz/api/v1/movies/docs

## Etape n°4 :
 - Fractionnement des fichiers pour rendre le code plus lisible
 - Transformation des deploiement pour les base de données en statefulset
 - Amélioration de la lisibilité du Jenkinsfile

## Etape n°5 :
 Gestion de la persistance des données 

## Etape FIX BUG 1 : 
 - Correction du Fichier docker-compose.yaml et du fichier nginx_config.conf afin de rediriger vers les bons ports en cas de déploiem>
 - On joint les services déployés par compose en allant aux adresses :
    - http://dockercompose.seb-coasne.cloudns.biz:8081/api/v1/movies/docs
    - http://dockercompose.seb-coasne.cloudns.biz:8081/api/v1/casts/docs
 - Remarques :
    - Inversion des ports 8001 et 8002 dans le docker compose pour rester cohérent avec ce que j'ai réalisé dans les fichiers hel>
    - Lorsqu'on crée un movie, on renssigne de cette façon :
      ```JSON
      {
        "name": "string",
        "plot": "string",
        "genres": [
          "string"
        ],
        "casts_id": [
          0
        ]
      }
      ```
    Si on souhaite compléter par un entier dans le tableau du champs "casts_id", il faut d'abord ajouter un cast avec l'id donné.

## Etape FIX BUG 2 : 
 - Rajout de la variable d'environnement CAST_SERVICE_HOST_URL au service movie_service pour qu'il puisse communiquer avec le service cast_service

## Etape n°6 :
 - Création des secrets pour movie-service et movie-db mais avec les valeurs (nom d'utilisateur, mdp, ...) toujours dans le code
 - Stockage des infos sensibles dans des fichiers qu'il suffira de rajouter dans le gitignore pour éviter d'apparaitre dans le depot, de télécharger sur le serveur pour pouvoir être utilisés 
	+ Suppression d'un fichier créé erreur values.yam
 - Création des secrets pour cast-service et cast-db

 ## Etape n°7 :
 - Ajout de 4 fichiers values pour gérer les déploiements dans les 4 namespaces différents
 - Le fichier values.yaml contiendra les valeurs communes aux 4 namespaces et les 4 autres fichiers pourront gérer les valeurs spécifiques aux différents namespaces
 - Mise à jour du Jenkinsfile pour déployer dans les 4 namespaces
 - Les services devoient être accessibles aux adresses suivantes :
    - http://dev.exam-jenkins.seb-coasne.cloudns.biz/api/v1/casts/docs
    - http://dev.exam-jenkins.seb-coasne.cloudns.biz/api/v1/movies/docs
    - http://qa.exam-jenkins.seb-coasne.cloudns.biz/api/v1/casts/docs
    - http://qa.exam-jenkins.seb-coasne.cloudns.biz/api/v1/movies/docs
    - http://staging.exam-jenkins.seb-coasne.cloudns.biz/api/v1/casts/docs
    - http://staging.exam-jenkins.seb-coasne.cloudns.biz/api/v1/movies/docs
    - http://prod.exam-jenkins.seb-coasne.cloudns.biz/api/v1/casts/docs
    - http://prod.exam-jenkins.seb-coasne.cloudns.biz/api/v1/movies/docs