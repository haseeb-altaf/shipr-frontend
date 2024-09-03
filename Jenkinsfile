pipeline {
    agent any
    
    // Parameters to allow developer to select branch
    parameters {
        gitParameter(
            name: 'BRANCH_NAME',
            type: 'PT_BRANCH',
            defaultValue: 'develop',
            description: 'Select the branch you want to deploy',
            branchFilter: 'origin/(.*)',
            selectedValue: 'DEFAULT',
            sortMode: 'ASCENDING'
        )
    }
    
    environment {
        DOCKER_IMAGE = "haseeb497/newimage:${BRANCH_NAME}"  // Tag image with branch name
        DOCKER_CREDENTIALS_ID = 'your-docker-hub-credentials-id'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the selected branch
                git branch: "${params.BRANCH_NAME}", url: 'https://github.com/haseeb-altaf/shipr-frontend.git'
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image
                sh "docker build -t ${env.DOCKER_IMAGE} ."
            }
        }

        stage('Docker Push') {
            steps {
                // Push Docker image to Docker Hub
                withCredentials([string(credentialsId: env.DOCKER_CREDENTIALS_ID, variable: 'DOCKERHUB_TOKEN')]) {
                    sh "echo $DOCKERHUB_TOKEN | docker login -u haseeb497 --password-stdin"
                    sh "docker push ${env.DOCKER_IMAGE}"
                }
            }
        }

        stage('Cleanup') {
            steps {
                // Clean up Docker images from the Jenkins agent
                sh "docker rmi ${env.DOCKER_IMAGE}"
            }
        }
    }
    
    post {
        success {
            echo "The branch ${params.BRANCH_NAME} has been successfully deployed!"
        }
        failure {
            echo "Deployment failed for branch ${params.BRANCH_NAME}."
        }
    }
}
