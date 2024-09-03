pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "haseeb497/project:frontend-${BRANCH_NAME}-${BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    def userBranch = input message: 'Select the branch to build', ok: 'Build', parameters: [string(name: 'BRANCH_NAME', defaultValue: 'develop', description: 'Branch to build')]
                    env.BRANCH_NAME = userBranch
                }

                checkout([$class: 'GitSCM', branches: [[name: "${BRANCH_NAME}"]], 
                userRemoteConfigs: [[url: 'https://github.com/haseeb-altaf/shipr-frontend.git']]])
            }
        }

        stage('Docker Build') {
            steps {

                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Push') {
            steps {

                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}
