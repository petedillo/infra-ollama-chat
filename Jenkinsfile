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
          sh 'docker build -t diolab:5000/ollama-frontend:latest ./frontend'
          sh 'docker build -t diolab:5000/ollama-backend:latest ./backend'
        }
      }
    }

    stage('Push Images') {
      steps {
        sh 'docker push diolab:5000/ollama-frontend:latest'
        sh 'docker push diolab:5000/ollama-backend:latest'
      }
    }

    stage('Cleanup Images on Jenkins Agent') {
      steps {
        sh 'docker rmi diolab:5000/ollama-frontend:latest || true'
        sh 'docker rmi diolab:5000/ollama-backend:latest || true'
      }
    }
  }
}
