pipeline {
  agent any

  tools {
    jdk 'jdk-11'
    maven 'mvn-3.6.3'
  }

  stages {
    stage('Build') {
      steps {
        mvn package
      }
    }
  }
  
  post {
    always {
      archive 'target/**/*.jar'
      junit 'target/**/*.xml'
    }
    success {
      withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
        mvn jib:build
      }
    }
  }
}
