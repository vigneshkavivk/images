pipeline {
    agent any

    environment {
        IMAGE_NAME = "dockerhubusername/python-app"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/username/python-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'USER',
                passwordVariable: 'PASS'
                )]) {

                sh '''
                docker login -u $USER -p $PASS
                docker push $IMAGE_NAME:latest
                '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }

    }
}
