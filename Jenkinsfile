pipeline {
  environment {
    registry = "rotema/my-webapp"
    registryCredential = 'DockerHub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Clone my git') {
      steps{
        git 'https://github.com/rotemad/my-webapp.git'
      }
    }
    stage('Image Build') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
    stage('Push the image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Stop & Delete a running container') {
      steps{
        sh 'docker ps -f name=webapp -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=webapp -q | xargs -r docker container rm'
      }
    }    
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry"
      }
    }
    stage('Pull and run my webapp') {
      steps{
        sh 'docker pull rotema/my-webapp'
        sh 'docker run --name webapp -p 5000:5000 -d rotema/my-webapp'
      }
    }    
  }
}