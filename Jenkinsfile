pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "yourdockerhubusername/myapp:latest"
    }
    stages {
        stage('Checkout') {
            steps { git url: 'https://github.com/frankchantu-boop/test', branch: 'master' }
        }
        stage('Build Docker Image') {
            steps { sh 'docker build -t $DOCKER_IMAGE .' }
        }
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
        stage('Deploy to Minikube/AWS EKS') {
            steps {
                sh "kubectl apply -f k8s-deployment.yaml && kubectl apply -f k8s-service.yaml"
            }
        }
    }
}
