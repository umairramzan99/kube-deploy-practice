pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        IMAGE_TAG = "latest"
        DEPLOY_YAML = "deployment.yaml"
        SERVICE_YAML = "service.yaml"
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
                          eval \$(minikube docker-env) && \
                          docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        """
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    // Deploy Redis persistent components first
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f redis-pv.yaml"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f redis-pvc.yaml"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f redis-deployment.yaml"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f redis-service.yaml"

                    // Deploy main app
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ App and Redis deployed! Visit your app using: minikube service visitor-service --url"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
