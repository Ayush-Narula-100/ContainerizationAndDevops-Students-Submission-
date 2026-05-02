pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "anoxi"
        IMAGE_NAME = "${DOCKERHUB_USER}/my-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Add your tests here"'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                    echo "$DOCKER_TOKEN" | docker login -u "$DOCKERHUB_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker rmi $IMAGE_NAME:$TAG || true'
            }
        }
    }
}