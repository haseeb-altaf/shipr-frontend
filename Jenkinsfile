pipeline {
    agent any

    // No need for manual branch selection since it's a multibranch pipeline
    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        DOCKER_HUB_REPO = 'haseeb497/project'
        IMAGE_NAME = "${DOCKER_HUB_REPO}:frontend-${env.BRANCH_NAME}"
        DOCKERFILE_PATH = "Dockerfile"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: "${env.BRANCH_NAME}"]], 
                          userRemoteConfigs: [[url: 'https://github.com/haseeb-altaf/shipr-frontend']]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME} using Dockerfile from ${DOCKERFILE_PATH}"
                    sh "docker build -t ${IMAGE_NAME} -f ${DOCKERFILE_PATH} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing Docker image: ${IMAGE_NAME} to Docker Hub"
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
