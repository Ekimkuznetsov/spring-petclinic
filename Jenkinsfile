pipeline {
  agent any
  environment {
    REGISTRY = "petclinic"
    registryCredential = credentials('dockerhub-petclinic')
    dockerImage = ''
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'petclinic', url: 'https://github.com/Ekimkuznetsov/spring-petclinic.git']])
      }
    }
    stage('Build') {
      steps {
        sh './mvnw package'   
      }
      post {
        always {
          sh 'mvn test'
        }
      }
    }
    stage('Artifact-creation') {
      steps{
        script {
          dockerImage = docker.build REGISTRY + ":latest"
        }
      }
    }
    stage('Deploy Image') {
      steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
  }
}

