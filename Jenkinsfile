pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = "ahmedsouilah/souilahahmed-5ds8-gestionprojets"
        IMAGE_TAG = "backend-${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ahmedsouilah33/5DS8-souilah.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Creation Image + Conteneur') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG ./backend'
                sh 'docker run -d --name test-container -p 8082:8080 $IMAGE_NAME:$IMAGE_TAG || true'
                sh 'sleep 5'
                sh 'docker rm -f test-container || true'
            }
        }

        stage('Credentials') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                sh 'docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Build & Push Frontend') {
            steps {
                sh 'docker build -t $IMAGE_NAME:frontend-$BUILD_NUMBER ./frontend'
                sh 'docker push $IMAGE_NAME:frontend-$BUILD_NUMBER'
                sh 'docker tag $IMAGE_NAME:frontend-$BUILD_NUMBER $IMAGE_NAME:frontend-latest'
                sh 'docker push $IMAGE_NAME:frontend-latest'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
