pipeline {
    agent any

    environment {
        APP_NAME = "flask-app"
        DOCKER_IMAGE = "flask-app"
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Checkout your Git repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image using Windows bat command
                bat """
                docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Apply Kubernetes manifests
                bat """
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                """
            }
        }

        stage('Switch Service to New Version') {
            steps {
                // Update the service to new image version
                bat """
                kubectl set image deployment/%APP_NAME% %APP_NAME%=%DOCKER_IMAGE%:%BUILD_NUMBER%
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for errors."
        }
    }
}
