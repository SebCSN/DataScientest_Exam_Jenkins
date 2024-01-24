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
