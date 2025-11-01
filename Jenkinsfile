pipeline {
  agent any

  environment {
    IMAGE_NAME     = "sakshi/jenkins-task2"   // change if you want to push to Docker Hub
    CONTAINER_NAME = "jenkins-task2-web"
    HOST_PORT      = "8090"
    CONTAINER_PORT = "80"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build (lint)') {
      steps {
        echo 'No compilation needed for static site.'
      }
    }

    stage('Test') {
      steps {
        sh '''
          if [ ! -f app/index.html ]; then
            echo "index.html missing!" >&2
            exit 1
          else
            echo "index.html present ✅"
          fi
        '''
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t ${IMAGE_NAME}:$BUILD_NUMBER .'
      }
    }

    stage('Deploy (Docker Run)') {
      steps {
        sh '''
          if [ "$(docker ps -aq -f name=${CONTAINER_NAME})" ]; then
            docker rm -f ${CONTAINER_NAME} || true
          fi
          docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:$BUILD_NUMBER
        '''
      }
    }
  }

  post {
    success {
      echo "Deployed at: http://localhost:${HOST_PORT}"
      echo "Image tag: ${IMAGE_NAME}:${BUILD_NUMBER}"
    }
    always {
      archiveArtifacts artifacts: '**/*.html', fingerprint: true
    }
  }
}
