pipeline {
  agent any

  environment {
    FRONTEND_REPO = 'https://github.com/petedillo/ollama-chat'
    BACKEND_REPO = 'https://github.com/petedillo/ollama-chat-api'
    REGISTRY = 'diolab:5000'
  }

  stages {
    stage('Clone Repos') {
      steps {
        dir('frontend') {
          git url: "${FRONTEND_REPO}", branch: 'main'
        }
        dir('backend') {
          git url: "${BACKEND_REPO}", branch: 'main'
        }
      }
    }

    stage('Build Docker Images') {
      steps {
        script {
          withCredentials([string(credentialsId: 'DIOCHAT_BASE_URL', variable: 'DIOCHAT_BASE_URL')]) {
            sh """
              docker build -t ${REGISTRY}/ollama-frontend:latest ./frontend \\
                  --build-arg VITE_API_BASE_URL=${DIOCHAT_BASE_URL}
            """
          }
          sh "docker build -t ${REGISTRY}/ollama-backend:latest ./backend"
        } 
      }
    }

    stage('Push Images') {
      steps {
        sh "docker push ${REGISTRY}/ollama-frontend:latest"
        sh "docker push ${REGISTRY}/ollama-backend:latest"
      }
    }

    stage('Cleanup Images on Jenkins Agent') {
      steps {
        sh "docker rmi ${REGISTRY}/ollama-frontend:latest || true"
        sh "docker rmi ${REGISTRY}/ollama-backend:latest || true"
      }
    }
  }
}
