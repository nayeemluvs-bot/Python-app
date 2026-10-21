pipeline {
  agent any

  environment {
    IMAGE_NAME = 'python-app'
    IMAGE_TAG  = 'v1'
    DOCKERHUB  = 'nayeemluvs'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Validate source') {
      steps {
        sh '''
          set -eu
          test -f Dockerfile
          echo "Dockerfile found"
          ls -la
        '''
      }
    }

    stage('Build image') {
      steps {
        sh 'docker build --pull -t "$DOCKERHUB/$IMAGE_NAME:$IMAGE_TAG" .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DH_USER',
          passwordVariable: 'DH_TOKEN'
        )]) {
          sh '''
            set +x
            echo "$DH_TOKEN" | docker login --username "$DH_USER" --password-stdin
            docker push "$DOCKERHUB/$IMAGE_NAME:$IMAGE_TAG"
            docker logout
          '''
        }
      }
    }
  }

  post {
    always {
      sh 'docker image ls "$DOCKERHUB/$IMAGE_NAME" || true'
    }
  }
}
