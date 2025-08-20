pipeline {
    agent any

    environment {
        APP_NAME = "flask-app"
        DOCKER_IMAGE = "flask-app"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Use Python installed on Windows
                bat 'python -m pip install --upgrade pip'
                bat 'pip install -r requirements.txt'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Make sure Docker Desktop is running on Windows
                bat "docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% ."
            }
        }

        stage('Push Docker Image') {
            steps {
                // Use your Docker Hub credentials stored in Jenkins
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat "docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_USER%/%DOCKER_IMAGE%:%BUILD_NUMBER%"
                    bat "docker push %DOCKER_USER%/%DOCKER_IMAGE%:%BUILD_NUMBER%"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Assuming kubectl is configured on Windows
                bat "kubectl apply -f k8s/deployment.yaml"
                bat "kubectl apply -f k8s/service.yaml"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            bat 'docker system prune -f'
        }
    }
}
