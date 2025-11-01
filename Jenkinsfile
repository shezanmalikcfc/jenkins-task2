pipeline {
  agent any

  environment {
    IMAGE_NAME = "sakshi/jenkins-task2"   // change to your Docker Hub name if you plan to push
    CONTAINER_NAME = "jenkins-task2-web"
    HOST_PORT = "8090"
    CONTAINER_PORT = "80"
  }

  stages {
    stage("Checkout") {
      steps {
        checkout scm
      }
    }

    stage("Build (lint)") {
      steps {
        echo "No compilation needed for static site."
        // put linters here if you add real code later
      }
    }

    stage("Test") {
      steps {
        // minimal sanity test: ensure index.html exists
        powershell """
          if (-Not (Test-Path .\\app\\index.html)) {
            Write-Error 'index.html missing!'
          } else {
            Write-Host 'index.html present ?'
          }
        """
      }
    }

    stage("Docker Build") {
      steps {
        sh 'docker build -t ${IMAGE_NAME}:$BUILD_NUMBER .'
      }
    }

    stage("Deploy (Docker Run)") {
      steps {
        // stop and remove any existing container with same name
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
