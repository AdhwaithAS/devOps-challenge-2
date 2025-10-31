pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    APP_NAME = 'flask-app'
    SERVICE_NAME = 'flask-service'
    IMAGE = 'adhwaithas/flask-app:latest'
    K8S_DEPLOY = 'deployment.yaml'
    K8S_SERVICE = 'service.yaml'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup Minikube') {
      steps {
        sh '''
          set -euo pipefail

          if ! command -v minikube >/dev/null 2>&1; then
            echo "ERROR: minikube not found on PATH" >&2
            exit 1
          fi
          if ! command -v kubectl >/dev/null 2>&1; then
            echo "ERROR: kubectl not found on PATH" >&2
            exit 1
          fi

          # Ensure minikube is running
          if ! minikube status >/dev/null 2>&1; then
            echo "Starting minikube..."
            minikube start
          fi

          # Use minikube context
          kubectl config use-context minikube
        '''
      }
    }

    stage('Build Docker image (in Minikube)') {
      steps {
        sh '''
          set -euo pipefail
          # Build image directly into minikube's Docker daemon
          eval $(minikube -p minikube docker-env)
          docker version
          echo "Building image: ${IMAGE}"
          docker build -t ${IMAGE} .
          echo "Built images:"
          docker images | grep -E "REPOSITORY|${APP_NAME}|${IMAGE}" || true
        '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          set -euo pipefail
          echo "Applying manifests..."
          kubectl apply -f ${K8S_DEPLOY}
          kubectl apply -f ${K8S_SERVICE}

          echo "Waiting for rollout of deployment/${APP_NAME}..."
          kubectl rollout status deployment/${APP_NAME} --timeout=180s

          echo "Current services:"
          kubectl get svc ${SERVICE_NAME} -o wide || true
        '''
      }
    }

    stage('Smoke Test') {
      steps {
        sh '''
          set -euo pipefail
          URL=$(minikube service ${SERVICE_NAME} --url)
          echo "Smoke testing at ${URL}"
          curl -sS ${URL}/ | tee response.json
          grep -q '"message":\\s*"Hello World"' response.json
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'response.json', allowEmptyArchive: true
    }
  }
}


