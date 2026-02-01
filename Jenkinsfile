pipeline {
  agent any

  environment {
    IMAGE = "dockerhubuser/webapp:${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build Image') {
      steps {
        sh "docker build -t $IMAGE ."
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'USER',
          passwordVariable: 'PASS'
        )]) {
          sh """
          docker login -u $USER -p $PASS
          docker push $IMAGE
          """
        }
      }
    }

    stage('Deploy') {
      steps {
        sh """
        sed -i 's|IMAGE_TAG|$IMAGE|g' deployment.yaml
        kubectl apply -f deployment.yaml
        """
      }
    }
  }
}

