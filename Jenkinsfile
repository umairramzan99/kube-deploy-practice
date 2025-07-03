pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        IMAGE_TAG = "latest"
        DOCKERHUB_USERNAME = "umair668"
        IMAGE_FULL_NAME = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        DEPLOY_YAML = "deployment.yaml"
        SERVICE_YAML = "service.yaml"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh """
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_FULL_NAME}
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_FULL_NAME}
                    """
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh "sed -i 's|image:.*|image: ${IMAGE_FULL_NAME}|' ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed and deployed from Docker Hub!"
        }
        failure {
            echo "❌ Something went wrong. Check logs."
        }
    }
}
