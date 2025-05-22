pipeline {
    agent any

    environment {
        IMAGE_NAME = "localhost:5000/demo-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm test || echo "No hay tests aún."'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t demo-app:latest ."
                sh "docker images | grep demo-app"
            }
        }

        stage('Push Image') {
            steps {
                sh """
                    docker tag demo-app:latest ${IMAGE_NAME}:${TAG}
                    docker push ${IMAGE_NAME}:${TAG}
                """
            }
        }

        stage('Deploy to K8s') {
            steps {
                sh """
                    kubectl apply -R -f k8s/demo-app/
                    kubectl set image deployment/demo-app demo-app=${IMAGE_NAME}:${TAG}
                """
            }
        }
    }
}
