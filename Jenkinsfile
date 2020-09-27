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
          System.setProperty("org.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL", "3800");
        }  
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
        sh 'ssh ubuntu@10.10.10.149 "docker ps -f name=webapp -q | xargs --no-run-if-empty docker container stop"'
        sh 'ssh ubuntu@10.10.10.149 "docker container ls -a -fname=webapp -q | xargs -r docker container rm"'
      }
    }    
    stage('Remove Unused docker image') {
      steps{
        sh 'ssh ubuntu@10.10.10.149 "docker rmi $registry"'
      }
    }
    stage('Pull and run my webapp') {
      steps{
        sh 'ssh ubuntu@10.10.10.149 "docker pull rotema/my-webapp"'
        sh 'ssh ubuntu@10.10.10.149 "docker run --name webapp -p 5000:5000 -d rotema/my-webapp"'
      }
    }    
  }
}