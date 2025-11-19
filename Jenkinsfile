pipeline {
    environment {
        DOCKER_ID = "baris1892"
        DOCKER_IMAGE_MOVIE_SERVICE = "datascientest-movie-service"
        DOCKER_IMAGE_CAST_SERVICE = "datascientest-cast-service"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        // we will tag our images with the current build in order to increment the value by 1 with each new build
    }

    agent any

    stages {

        stage('Docker Build') {
            steps {
                script {
                    // Movie Service
                    sh "docker build -t $DOCKER_ID/$DOCKER_IMAGE_MOVIE_SERVICE:$DOCKER_TAG ./movie-service"

                    // Cast Service
                    sh "docker build -t $DOCKER_ID/$DOCKER_IMAGE_CAST_SERVICE:$DOCKER_TAG ./cast-service"

                    // sleep 6
                }
            }
        }

        stage('Docker Run & Test Movie Service') {
            steps {
                script {
                    sh """
            docker run -d --name movie-test -p 8081:8000 $DOCKER_ID/$DOCKER_IMAGE_MOVIE_SERVICE:$DOCKER_TAG
            sleep 10
            curl --verbose --fail http://localhost:8081/api/v1/checkapi
            docker stop movie-test && docker rm movie-test
            """
                }
            }
        }

        stage('Docker Run & Test Cast Service') {
            steps {
                script {
                    sh """
            docker run -d --name cast-test -p 8082:8000 $DOCKER_ID/$DOCKER_IMAGE_CAST_SERVICE:$DOCKER_TAG
            sleep 10
            curl --verbose --fail http://localhost:8082/api/v1/checkapi
            docker stop cast-test && docker rm cast-test
            """
                }
            }
        }

        stage('Docker Push') {
            environment {
                // we retrieve docker password from secret text called docker_hub_pass saved on jenkins
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                script {
                    sh """
                        docker login -u $DOCKER_ID -p $DOCKER_PASS

                        docker push $DOCKER_ID/$DOCKER_IMAGE_MOVIE_SERVICE:$DOCKER_TAG
                        docker push $DOCKER_ID/$DOCKER_IMAGE_CAST_SERVICE:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Deployment in dev') {
            environment {
                KUBECONFIG = credentials("config")
                // we retrieve kubeconfig from secret file called config saved on jenkins
            }

            steps {
                script {
                    sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                
                # Cast Service
                cp charts/values-cast-service.yaml values-cast-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-cast-service.yml
                helm upgrade --install cast-service ./charts -f values-cast-service.yml --namespace dev

                # Movie Service
                cp charts/values-movie-service.yaml values-movie-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-movie-service.yml
                helm upgrade --install movie-service ./charts -f values-movie-service.yml --namespace dev
                '''
                }
            }
        }

        stage('Deployment in qa') {
            environment {
                KUBECONFIG = credentials("config")
                // we retrieve kubeconfig from secret file called config saved on jenkins
            }
            steps {
                script {
                    sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                
                # Cast Service
                cp charts/values-cast-service.yaml values-cast-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-cast-service.yml
                helm upgrade --install cast-service ./charts -f values-cast-service.yml --namespace qa

                # Movie Service
                cp charts/values-movie-service.yaml values-movie-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-movie-service.yml
                helm upgrade --install movie-service ./charts -f values-movie-service.yml --namespace qa
                '''
                }
            }
        }

        stage('Deployment in staging') {
            environment {
                KUBECONFIG = credentials("config")
                // we retrieve kubeconfig from secret file called config saved on jenkins
            }
            steps {
                script {
                    sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                
                # Cast Service
                cp charts/values-cast-service.yaml values-cast-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-cast-service.yml
                helm upgrade --install cast-service ./charts -f values-cast-service.yml --namespace staging

                # Movie Service
                cp charts/values-movie-service.yaml values-movie-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-movie-service.yml
                helm upgrade --install movie-service ./charts -f values-movie-service.yml --namespace staging
                '''
                }
            }
        }

        stage('Deployment in prod') {
            environment {
                KUBECONFIG = credentials("config")
                // we retrieve kubeconfig from secret file called config saved on jenkins
            }
            steps {
                // Create an Approval Button with a timeout of 15minutes.
                // this require a manuel validation in order to deploy on production environment
                timeout(time: 15, unit: "MINUTES") {
                    input message: 'Do you want to deploy in production ?', ok: 'Yes'
                }

                script {
                    sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                
                # Cast Service
                cp charts/values-cast-service.yaml values-cast-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-cast-service.yml
                helm upgrade --install cast-service ./charts -f values-cast-service.yml --namespace prod

                # Movie Service
                cp charts/values-movie-service.yaml values-movie-service.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values-movie-service.yml
                helm upgrade --install movie-service ./charts -f values-movie-service.yml --namespace prod
                '''
                }
            }
        }

    }
}
