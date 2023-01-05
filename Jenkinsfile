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
        echo "Running ${VERSION} on ${env.JENKINS_URL}"
        sh "docker build -t ${NAME} ."
        sh "docker tag ${NAME}:latest ${IMAGE_REPO}/${NAME}:${VERSION}"
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

