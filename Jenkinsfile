pipeline {
  agent any
  environment {
    NAME = "petclinic"
    VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
    IMAGE = "${NAME}:${VERSION}"
    registry = "Ekimkuznetsov/petclinic"
    registryCredential = 'dockerhub-petclinic'
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
    }
    stage('Artifact-creation') {
      steps {
        script {
          dockerImage = docker.build -t ${NAME} .
      }
    }
    stage('Deploy Image') {
      steps {
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
  }
}

