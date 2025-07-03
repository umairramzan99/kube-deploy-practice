pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        IMAGE_TAG = "latest"
        DEPLOY_YAML = "deployment.yaml"
        SERVICE_YAML = "service.yaml"
        REDIS_DEPLOY = "redis-deployment.yaml"
        REDIS_SERVICE = "redis-service.yaml"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
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
                        sh """
                            eval \$(minikube docker-env)
                            docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        """
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_DEPLOY}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_SERVICE}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ App deployed! Visit: http://localhost:30080"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}

