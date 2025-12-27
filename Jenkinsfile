pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "frankchantu/myapp:latest" // <-- apna DockerHub username
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
                    try {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                    } catch (err) {
                        error "Docker build failed: ${err}"
                    }
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
                        try {
                            sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                            sh "docker push ${DOCKER_IMAGE}"
                        } catch (err) {
                            error "Docker push failed: ${err}. Check credentials or token."
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    try {
                        // Optional: Minikube cluster
                        sh "kubectl apply -f k8s-deployment.yaml"
                        sh "kubectl apply -f k8s-service.yaml"
                    } catch (err) {
                        echo "Kubernetes deployment skipped or failed: ${err}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed! Check logs above.'
        }
    }
}

