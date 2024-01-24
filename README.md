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
