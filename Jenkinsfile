pipeline {
    agent any

    environment {
        IMAGE_NAME = "kanasani1/webdite"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"  // replace with your Jenkins credentials ID
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/anjudoll/webdite.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}", ".")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Local Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f manifest/deployment.yaml"
                    sh "kubectl apply -f manifest/service.yaml"
                }
            }
        }
    }

    post {
        success {
            echo '🚀 Deployment to local Kubernetes succeeded!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
