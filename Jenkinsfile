pipeline {
    agent any
    tools {
        git 'Default'
    }

    environment {
        DOCKER_IMAGE = 'adhwaithas/flask-app'
        KUBERNETES_NAMESPACE = 'default'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from Git...'
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image for ${APP_DIR}..."
                script {
                    dir(APP_DIR) {
                        sh """
                            docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                            docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                        """
                    }
                }
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                script {
                    dir(APP_DIR) {
                        withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                        usernameVariable: 'DOCKER_USER', 
                                        passwordVariable: 'DOCKER_PASS')]) {
                            sh """
                                echo \${DOCKER_PASS} | docker login -u \${DOCKER_USER} --password-stdin
                                docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                                docker push ${DOCKER_IMAGE}:latest
                            """
                        }
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying Flask app to Kubernetes cluster...'
                script {
                    dir(APP_DIR) {
                        sh """
                            # Deploy the application
                            kubectl apply -f deployment.yaml
                            kubectl apply -f service.yaml
                            
                            # Wait for rollout to complete
                            kubectl rollout status deployment/flask-app -n ${KUBERNETES_NAMESPACE}
                        """
                    }
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                echo 'Verifying deployment status...'
                script {
                    sh """
                        echo '=== PODS STATUS ==='
                        kubectl get pods -l app=flask-app -n ${KUBERNETES_NAMESPACE}
                        
                        echo '=== SERVICE STATUS ==='
                        kubectl get svc flask-service -n ${KUBERNETES_NAMESPACE}
                        
                        echo '=== DEPLOYMENT STATUS ==='
                        kubectl get deployment flask-app -n ${KUBERNETES_NAMESPACE}
                    """
                }
            }
        }
        
        stage('Health Check') {
            steps {
                echo 'Running application health check...'
                script {
                    sh """
                        sleep 10
                        POD_NAME=\$(kubectl get pods -l app=flask-app -n ${KUBERNETES_NAMESPACE} -o jsonpath='{.items[0].metadata.name}')
                        
                        if [ -n "\$POD_NAME" ]; then
                            echo "Checking health of pod: \$POD_NAME"
                            kubectl exec \$POD_NAME -n ${KUBERNETES_NAMESPACE} -- curl -f http://localhost:5000/health || exit 1
                            echo "Health check passed!"
                        else
                            echo "No pods found!"
                            exit 1
                        fi
                    """
                }
            }
        }
        
        stage('Service URL') {
            steps {
                echo 'Getting service URL...'
                script {
                    sh """
                        echo '=== ACCESSING THE APPLICATION ==='
                        kubectl get svc flask-service -n ${KUBERNETES_NAMESPACE}
                        echo 'Application is accessible on NodePort 30007'
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline succeeded! Flask app deployed successfully.'
            echo "Docker Image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            echo "DockerHub: docker.io/${DOCKER_IMAGE}:latest"
        }
        failure {
            echo '❌ Pipeline failed! Check logs above for details.'
            script {
                // Get logs from failed pods
                sh """
                    echo '=== GETTING POD LOGS ==='
                    kubectl get pods -l app=flask-app -n ${KUBERNETES_NAMESPACE}
                    kubectl logs -l app=flask-app -n ${KUBERNETES_NAMESPACE} --tail=50 || true
                """
            }
        }
        always {
            echo 'Cleaning up...'
            sh 'docker logout || true'
        }
    }
}

