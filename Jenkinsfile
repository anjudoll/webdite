pipeline {
    agent any

    environment {
        IMAGE_NAME = 'kanasani1/suma2'
    }

    stages {
        stage('Checkout') {
            steps {
               
                git 'https://github.com/anjudoll/jenkins.git'
            }
            
        }

        stage('Build Docker Image') {
            steps {
                dir('src') {
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
              
                sh 'kubectl apply -f manifest/deployment.yaml'
                sh 'kubectl apply -f manifest/service.yaml'
            }
        }
    }
}

