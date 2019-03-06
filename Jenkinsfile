pipeline {
  environment {
    registry = "eaingaran/node-todo-frontend"
    registryCredential = '18764365-1a9c-45f3-a779-900c4452d870'
    dockerImage = ''
    containerId = sh(script: 'docker ps -aqf "name=node-app"', returnStdout: true)
  }
  agent any
  tools {nodejs "nodejs" }
  stages {
    stage('Build') {
       steps {
         sh 'npm install'
       }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image') {
      steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Cleanup') {
      when {
                not { environment ignoreCase: true, name: 'containerId', value: '' }
        }
      steps {
        sh 'docker stop ${containerId}'
        sh 'docker rm ${containerId}'
      }
    }
    stage('Run Container') {
      steps {
        sh 'docker run --name=node-app -d -p 9000:9000 $registry:$BUILD_NUMBER &'
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
