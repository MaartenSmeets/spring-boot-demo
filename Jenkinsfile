pipeline {
  agent any

  tools {
    jdk 'jdk-11'
    maven 'mvn-3.6.3'
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn package'
      }
    }
  }
  
  post {
    always {
      archive 'target/**/*.jar'
      junit 'target/**/*.xml'
    }
    success {
      withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
        sh 'mvn jib:build -Djib.to.auth.username=${USERNAME} -Djib.to.auth.password=${PASSWORD}'
      }
    }
  }
}
