pipeline {
  agent any

  tools {
    jdk 'jdk-11'
    maven 'mvn-3.6.3'
  }

  stages {
    stage('Build') {
      steps {
        withMaven {
          sh "mvn package"
        }
      }
    }
  }
  
  post {
    success {
      withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
        sh "mvn jib:build -Djib.to.auth.username=$DOCKER_USERNAME -Djib.to.auth.password=$DOCKER_PASSWORD"
      }
    }
  }
}
