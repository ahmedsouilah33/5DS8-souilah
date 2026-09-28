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
                sh 'docker run -d --rm --name test-container -p 8082:8080 $IMAGE_NAME:$IMAGE_TAG'
                sh 'sleep 10'
                sh 'docker stop test-container'
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
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
