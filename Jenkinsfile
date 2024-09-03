pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "haseeb497/project:${BRANCH_NAME}-${BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Let the developer select the branch to build
                script {
                    def userBranch = input message: 'Select the branch to build', ok: 'Build', parameters: [string(name: 'BRANCH_NAME', defaultValue: 'develop', description: 'Branch to build')]
                    env.BRANCH_NAME = userBranch
                }
                // Checkout the selected branch
                checkout([$class: 'GitSCM', branches: [[name: "${BRANCH_NAME}"]], 
                userRemoteConfigs: [[url: 'https://github.com/haseeb-altaf/shipr-frontend.git']]])
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Push') {
            steps {
                // Push Docker image to Docker Hub
                withCredentials([string(credentialsId: env.DOCKER_CREDENTIALS_ID, variable: 'DOCKERHUB_TOKEN')]) {
                    sh "echo $DOCKERHUB_TOKEN | docker login -u haseeb497 --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}
