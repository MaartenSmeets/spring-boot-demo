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
    
    stage('Make Container') {
      steps {
      sh "docker build -t maartensmeets/spring-boot-demo:${env.BUILD_ID} ."
      sh "docker tag maartensmeets/spring-boot-demo:${env.BUILD_ID} maartensmeets/spring-boot-demo:latest"
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
        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
        sh "docker push maartensmeets/spring-boot-demo:${env.BUILD_ID}"
        sh "docker push maartensmeets/spring-boot-demo:latest"
      }
    }
  }
}
