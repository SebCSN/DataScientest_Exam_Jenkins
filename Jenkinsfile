pipeline {
    environment { // Declaration des variables d'environment
        DOCKER_ID = "dockersebc"
        DOCKER_TAG = "v.1.${BUILD_ID}" // On marque les images avec la valeur actuelle du build afin d'incrémenter la valeur de 1 à chaque nouvelle version.
    }

    agent any // Jenkins pourra sélectionner tous les agents disponibles
    
    stages {
        stage('Docker build'){ // Etape de construction des images Docker
            parallel {
                stage('Docker Build movie_service'){ // Etape de construction de l'image docker movie_service
                    steps {
                        script {
                            sh '''
                            cd ./movie-service
                            docker rm -f movie_service
                            docker rmi -f $DOCKER_ID/movie_service:latest || true
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
                            docker rmi -f $DOCKER_ID/cast_service:latest || true
                            docker build -t $DOCKER_ID/cast_service:$DOCKER_TAG .
                            sleep 6
                            '''
                        }
                    }
                }
            }
        }

        stage('Exécution docker compose up -d'){ // Etape d'éxécution du déploiement
            steps {
                script {
                    sh '''
                    docker compose up -d
                    sleep 10
                    '''
                }
            }
        }

        stage('Test acceptation'){ // Vérification du bon fonctionnement
            parallel {
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
            }
        }

        stage('Push des images sur Docker Hub'){
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // On récupére le mot de passe docker à partir du texte secret appelé docker_hub_pass sauvegardé sur jenkins
            }

            parallel {
                stage('Docker Push movie_service'){ // On transmet l'image movie_service construite à notre compte docker hub
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

        stage('Arret du service lancé par le docker-compose'){ 
            steps {
                script {
                    sh '''
		    docker compose down
                    docker rm -f datascientest_exam_jenkins-movie_service-1
                    docker rm -f datascientest_exam_jenkins-cast_service-1
                    docker rm -f datascientest_exam_jenkins-nginx-1
                    docker rmi -f exam-jenkins-movie_service:latest
                    docker rmi -f exam-jenkins-cast_service:latest
                    docker rmi -f $DOCKER_ID/movie_service:$DOCKER_TAG
                    docker rmi -f $DOCKER_ID/cast_service:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploiement') {
            environment {
                KUBECONFIG = credentials("configkubernetes") // On récupère configkubernetes à partir d'un fichier secret appelé config sauvegardé sur Jenkins
            }

            stages{
                stage('Deploiement en dev'){
                    steps {
                        script {
                            sh '''
                            rm -Rf .kube
                            mkdir .kube
                            cat $KUBECONFIG > .kube/config
                            cp examjenkins/values.yaml values.yml
                            cat values.yml
                            sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                            helm upgrade --install app examjenkins -f values.yml --namespace dev
                            '''
                        }
                    }
                }
            }
        }
    }
}
