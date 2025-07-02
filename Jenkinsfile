pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        IMAGE_TAG = "latest"
        DEPLOY_YAML = "deployment.yaml"
        SERVICE_YAML = "service.yaml"
    }

    stages {
        stage('Use Minikube Docker Daemon') {
            steps {
                script {
                    sh 'eval $(minikube docker-env)'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('app') {
                        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh "kubectl apply -f ${DEPLOY_YAML}"
                    sh "kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "App deployed! Visit: http://localhost:30080"
        }
    }
}

