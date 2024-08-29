pipeline {
    agent any

    parameters {
        gitBranch(name: 'BRANCH_NAME', description: 'Select branch to build')
    }

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        DOCKER_HUB_REPO = 'haseeb497/project'
        IMAGE_NAME = "${DOCKER_HUB_REPO}:${params.BRANCH_NAME}" // Image name with branch tag
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Checking out branch: ${params.BRANCH_NAME}"
                    checkout([$class: 'GitSCM', 
                              branches: [[name: "*/${params.BRANCH_NAME}"]],
                              userRemoteConfigs: [[url: 'https://github.com/haseeb-altaf/shipr-frontend.git']]
                    ])
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME}"
                    sh "docker build -t ${IMAGE_NAME} ."
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
