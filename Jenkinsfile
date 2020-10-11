pipeline {
  agent any

  tools {
    jdk 'jdk-11'
    maven 'mvn-3.6.3'
  }

  stages {
    stage('Build') {
      steps {
        withMaven(maven : 'mvn-3.6.3') {
          sh "mvn package"
        }
      }
    }
    stage('SonarQube analysis') {
        withSonarQubeEnv(credentialsId: 'sonarqube-secret', installationName: 'My SonarQube Server') {
          sh 'mvn sonar:sonar'
        }
    }

    stage('Create and push container') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "mvn jib:build"
        }
      } 
    }
    stage('Anchore analyse') {
      steps {
        writeFile file: 'anchore_images', text: 'docker.io/maartensmeets/spring-boot-demo'
        anchore name: 'anchore_images'
      }
    }
    stage('Deploy to K8s') {
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
          sh 'chmod u+x ./kubectl'
          sh './kubectl apply -f k8s.yaml'
        }
      } 
    }
  }
}
