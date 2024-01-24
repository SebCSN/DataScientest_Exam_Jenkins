pipeline {
    environment { // Declaration des variables d'environment
        DOCKER_ID = "dockersebc"
        DOCKER_TAG = "v.1.${BUILD_ID}" // On marque les images avec la valeur actuelle du build afin d'incrémenter la valeur de 1 à chaque nouvelle version.
    }

    agent any // Jenkins pourra sélectionner tous les agents disponibles
    
    stages {
        stage('Docker Build movie_service'){ // Etape de construction de l'image docker movie_service
            steps {
                script {
                    sh '''
                    cd ./movie-service
                    docker rm -f movie_service
                    docker build -t $DOCKER_ID/movie_service:$DOCKER_TAG .
                    sleep 6
                    '''
                }
            }
        }

        stage('Docker Build cast_service'){ // Etape de construction de l'image docker cast_service
            steps {
                script {
                    sh '''
                    cd ./cast-service
                    docker rm -f cast_service
                    docker build -t $DOCKER_ID/cast_service:$DOCKER_TAG .
                    sleep 6
                    '''
                }
            }
        }

        stage('docker compose up -d'){ // Etape d'éxécution du déploiement
            steps {
                script {
                    sh '''
                    docker compose up -d
                    sleep 10
                    '''
                }
            }
        }

        stage('Test acceptation movie_service'){ // On lance la commande curl pour valider que le conteneur movie_service répond à la requête
            steps {
                script {
                    sh '''
                    curl http://localhost:8080/api/v1/movies/docs
                    '''
                }
            }
        }

        stage('Test acceptation cast_service'){ // On lance la commande curl pour valider que le conteneur cast_service répond à la requête
            steps {
                script {
                    sh '''
                    curl http://localhost:8080/api/v1/casts/docs
                    '''
                }
            }
        }

        stage('Arret du service lancé par le docker-compose'){ 
            steps {
                script {
                    sh '''
		    docker compose down
                    docker rm -f datascientest_exam_jenkins-movie_service-1
                    docker rm -f datascientest_exam_jenkins-movie_service-1
                    docker rm -f datascientest_exam_jenkins-nginx-1
                    '''
                }
            }
        }

        stage('Docker Push movie_service'){ // On transmet l'image movie_service construite à notre compte docker hub
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // On récupére le mot de passe docker à partir du texte secret appelé docker_hub_pass sauvegardé sur jenkins
            }

            steps {
                script {
                    sh '''
                    docker login -u $DOCKER_ID -p $DOCKER_PASS
                    docker push $DOCKER_ID/movie_service:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Docker Push cast_service'){ // On transmet l'image cast_service construite à notre compte docker hub
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // On récupére le mot de passe docker à partir du texte secret appelé docker_hub_pass sauvegardé sur jenkins
            }

            steps {
                script {
                    sh '''
                    docker login -u $DOCKER_ID -p $DOCKER_PASS
                    docker push $DOCKER_ID/cast_service:$DOCKER_TAG
                    '''
                }
            }
        }
    }
}
