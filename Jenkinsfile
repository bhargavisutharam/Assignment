pipeline {
    agent any

    environment {
        APP_NAME = "flask-app"
        DOCKER_IMAGE = "flask-app"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/bhargavisutharam/Assignment.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Choose blue or green lane based on build number
                    def version = (env.BUILD_NUMBER.toInteger() % 2 == 0) ? "green" : "blue"
                    env.DEPLOY_VERSION = version

                    sh """
                        eval \$(minikube docker-env)
                        echo "Building Docker image: ${DOCKER_IMAGE}:${DEPLOY_VERSION}"
                        docker build -t ${DOCKER_IMAGE}:${DEPLOY_VERSION} .
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        kubectl apply -f k8s/deployment_${DEPLOY_VERSION}.yaml
                        kubectl apply -f k8s/service.yaml
                        kubectl rollout status deployment/${APP_NAME}-${DEPLOY_VERSION} --timeout=120s
                    """
                }
            }
        }

        stage('Switch Service to New Version') {
            steps {
                script {
                    sh """
                        echo "Switching Service to ${DEPLOY_VERSION}"
                        kubectl patch service ${APP_NAME}-service -p '{"spec":{"selector":{"app":"${APP_NAME}","version":"${DEPLOY_VERSION}"}}}'
                    """
                }
            }
        }
    }
}
