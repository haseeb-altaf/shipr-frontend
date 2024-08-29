pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' 
        DOCKER_HUB_REPO = 'haseeb497/project' 
        IMAGE_NAME = "${DOCKER_HUB_REPO}:frontend-develop" 
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the current branch
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME}"
                    // Build the Docker image
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing Docker image: ${IMAGE_NAME} to Docker Hub"
                    // Login to Docker Hub and push the image
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                        sh "docker push ${IMAGE_NAME}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully. Docker image ${IMAGE_NAME} has been pushed to Docker Hub."
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
