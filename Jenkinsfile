pipeline {
    agent any

    environment {
        // Variable ko side pe rakhein, seedha username use karte hain error se bachne ke liye
        IMAGE_NAME = "frankchantu/myapp:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/frankchantu-boop/test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Yahan variable use ho raha hai
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials-id', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        // SINGLE QUOTES login ke liye
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        // PUSH command
                        sh "docker push ${IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f k8s-deployment.yaml"
                    sh "kubectl apply -f k8s-service.yaml"
                }
            }
        }
    }
}
