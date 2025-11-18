pipeline {
    environment {
        DOCKER_ID = "baris1892"
        DOCKER_IMAGE_MOVIE_SERVICE = "datascientest-movie-service"
        DOCKER_IMAGE_CAST_SERVICE = "datascientest-cast-service"
        // DOCKER_TAG = "v.${BUILD_ID}.0"
        // we will tag our images with the current build in order to increment the value by 1 with each new build

        DOCKER_TAG = "develop"
    }

    agent any

    stages {

        stage('Docker Build') {
            steps {
                script {
                    // Movie Service
                    sh "docker build -t $DOCKER_ID/$DOCKER_IMAGE_MOVIE_SERVICE:$BUILD_TAG ./movie-service"

                    // Cast Service
                    sh "docker build -t $DOCKER_ID/$DOCKER_IMAGE_CAST_SERVICE:$BUILD_TAG ./cast-service"

                    // sleep 6
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

                        docker push $DOCKER_ID/$DOCKER_IMAGE_MOVIE_SERVICE:$BUILD_TAG
                        docker push $DOCKER_ID/$DOCKER_IMAGE_CAST_SERVICE:$BUILD_TAG
                    """
                }
            }
        }

//        stage('Deployment in dev') {
//            environment {
//                KUBECONFIG = credentials("config")
//                // we retrieve kubeconfig from secret file called config saved on jenkins
//            }
//
//            steps {
//                script {
//                    sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace dev
//                '''
//                }
//            }
//        }
//
//        stage('Deployment in staging') {
//            environment {
//                KUBECONFIG = credentials("config")
//                // we retrieve kubeconfig from secret file called config saved on jenkins
//            }
//            steps {
//                script {
//                    sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace staging
//                '''
//                }
//            }
//        }
//
//        stage('Deployment in prod') {
//            environment {
//                KUBECONFIG = credentials("config")
//                // we retrieve kubeconfig from secret file called config saved on jenkins
//            }
//            steps {
//                // Create an Approval Button with a timeout of 15minutes.
//                // this require a manuel validation in order to deploy on production environment
//                timeout(time: 15, unit: "MINUTES") {
//                    input message: 'Do you want to deploy in production ?', ok: 'Yes'
//                }
//
//                script {
//                    sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace prod
//                '''
//                }
//            }
//        }

    }
}
