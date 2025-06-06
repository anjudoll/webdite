pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "kanasani1/webdite"
        IMAGE_TAG = "1.2.3"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/anjudoll/webdite.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker version"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to Local Kubernetes') {
            steps {
                sh "kubectl apply -f manifests/deploy.yaml"
                sh "kubectl apply -f manifests/serve.yaml"
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
