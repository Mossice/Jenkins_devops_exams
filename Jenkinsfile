pipeline {
    agent any

    environment {
        DOCKER_ID = "mossice"
        MOVIE_IMAGE = "movie-service"
        CAST_IMAGE  = "cast-service"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        WORKSPACE = "/home/vagrant"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t $DOCKER_ID/$MOVIE_IMAGE:$DOCKER_TAG ./movie-service
                docker build -t $DOCKER_ID/$CAST_IMAGE:$DOCKER_TAG ./cast-service
                '''
            }
        }

        stage('Test docker-compose') {
            steps {
                sh '''
                docker compose down -v || true
                docker compose up -d --build
                sleep 20

                curl -I http://localhost:8001/api/v1/movies/docs
                curl -I http://localhost:8002/api/v1/casts/docs

                docker compose down -v
                '''
            }
        }

        stage('Docker Push') {
            environment {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_ID" --password-stdin

                docker push $DOCKER_ID/$MOVIE_IMAGE:$DOCKER_TAG
                docker push $DOCKER_ID/$CAST_IMAGE:$DOCKER_TAG
                '''
            }
        }

        stage('Prepare Kubernetes') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                sh '''
                ls "$WORKSPACE"/.kube || mkdir -p "$WORKSPACE"/.kube
                export KUBECONFIG="$WORKSPACE"/.kube/config
                cat "$KUBECONFIG" > .kube/config

                k3s kubectl get ns dev || k3s kubectl create ns dev
                k3s kubectl get ns qa || k3s kubectl create ns qa
                k3s kubectl get ns staging || k3s kubectl create ns staging
                k3s kubectl get ns prod || k3s kubectl create ns prod
                '''
            }
        }

        stage('Deploy DEV') {
            when {
                branch 'develop'
            }
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                sh '''
                export KUBECONFIG="$WORKSPACE"/.kube/config
                cat "$KUBECONFIG" > .kube/config

                helm upgrade --install movieapp-dev ./charts -n dev \
                  --set movie.image.repository=$DOCKER_ID/$MOVIE_IMAGE \
                  --set movie.image.tag=$DOCKER_TAG \
                  --set cast.image.repository=$DOCKER_ID/$CAST_IMAGE \
                  --set cast.image.tag=$DOCKER_TAG \
                  --set movie.service.nodePort=30081 \
                  --set cast.service.nodePort=30082
                '''
            }
        }

        stage('Deploy QA') {
            when {
                branch 'qa'
            }
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                sh '''  
                export KUBECONFIG="$WORKSPACE"/.kube/config
                cat "$KUBECONFIG" > .kube/config

                helm upgrade --install movieapp-qa ./charts -n qa \
                  --set movie.image.repository=$DOCKER_ID/$MOVIE_IMAGE \
                  --set movie.image.tag=$DOCKER_TAG \
                  --set cast.image.repository=$DOCKER_ID/$CAST_IMAGE \
                  --set cast.image.tag=$DOCKER_TAG \
                  --set movie.service.nodePort=30083 \
                  --set cast.service.nodePort=30084
                '''
            }
        }

        stage('Deploy STAGING') {
            when {
                branch 'staging'
            }
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                sh '''
                export KUBECONFIG="$WORKSPACE"/.kube/config
                cat "$KUBECONFIG" > .kube/config

                helm upgrade --install movieapp-staging ./charts -n staging \
                  --set movie.image.repository=$DOCKER_ID/$MOVIE_IMAGE \
                  --set movie.image.tag=$DOCKER_TAG \
                  --set cast.image.repository=$DOCKER_ID/$CAST_IMAGE \
                  --set cast.image.tag=$DOCKER_TAG \
                  --set movie.service.nodePort=30085 \
                  --set cast.service.nodePort=30086
                '''
            }
        }

        stage('Validation PROD') {
            when {
                branch 'master'
            }
            steps {
                input message: "Confirmer le déploiement manuel en production depuis master ?"
            }
        }

        stage('Deploy PROD') {
            when {
                branch 'master'
            }
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                sh '''
                export KUBECONFIG="$WORKSPACE"/.kube/config
                cat "$KUBECONFIG" > .kube/config

                helm upgrade --install movieapp-prod ./charts -n prod \
                  --set movie.image.repository=$DOCKER_ID/$MOVIE_IMAGE \
                  --set movie.image.tag=$DOCKER_TAG \
                  --set cast.image.repository=$DOCKER_ID/$CAST_IMAGE \
                  --set cast.image.tag=$DOCKER_TAG \
                  --set movie.service.nodePort=30087 \
                  --set cast.service.nodePort=30088
                '''
            }
        }
    }

    post {
        always {
            sh 'docker compose down -v || true'
        }

        failure {
            echo "Pipeline en erreur."
        }

        success {
            echo "Pipeline terminé avec succès."
        }
    }
}
