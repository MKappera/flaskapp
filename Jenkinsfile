pipeline {
  agent any
  environment {
    IMAGE = "mkappera/flaskapp"
    REGISTRY_CRED = 'dockerhub-creds' // Jenkins credentials id for Docker Hub
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build') {
      steps { sh 'docker build -t ${IMAGE}:${BUILD_NUMBER} .' }
    }
    stage('Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: env.REGISTRY_CRED, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          sh 'docker push ${IMAGE}:${BUILD_NUMBER}'
          sh 'docker tag ${IMAGE}:${BUILD_NUMBER} ${IMAGE}:latest'
          sh 'docker push ${IMAGE}:latest'
        }
      }
    }
    stage('Deploy') {
      steps {
        sh 'docker stop flaskapp || true'
        sh 'docker rm flaskapp || true'
        sh "docker run -d --name flaskapp -p 5001:5000 ${IMAGE}:${BUILD_NUMBER}"
      }
    }
  }
}

