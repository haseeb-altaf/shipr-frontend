pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH_NAME', choices: ['main', 'develop'], description: 'Select branch to build')
    }

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // Jenkins credentials ID for Docker Hub
        DOCKER_HUB_REPO = 'haseeb497/project' // Your Docker Hub repository
        IMAGE_NAME = "${DOCKER_HUB_REPO}:${env.BRANCH_NAME}" // Image name with branch tag
        DOCKERFILE_PATH = "Dockerfile" // Path to Dockerfile in the root directory
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the selected branch
                checkout([$class: 'GitSCM', 
                          branches: [[name: "${params.BRANCH_NAME}"]], 
                          userRemoteConfigs: [[url: 'https://github.com/haseeb-altaf/shipr-frontend']]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME} using Dockerfile from ${DOCKERFILE_PATH}"
                    // Check if Dockerfile exists in the path
                    sh "ls -la ${DOCKERFILE_PATH}"
                    // Build the Docker image
                    sh "docker build -t ${IMAGE_NAME} -f ${DOCKERFILE_PATH} ."
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
