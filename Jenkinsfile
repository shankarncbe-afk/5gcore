pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shan2711/devops-eks-practice"
        DOCKER_TAG = "v3"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh 'kubectl set image deployment/nginx-practice nginx-container=$DOCKER_IMAGE:$DOCKER_TAG'
                sh 'kubectl rollout status deployment/nginx-practice'
            }
        }
    }
}
