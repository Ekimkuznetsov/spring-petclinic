pipeline {
    agent any
    options{
        buildDiscarder(logRotator(numToKeepStr: '5', daysToKeepStr: '5'))
        timestamps()
    }
    environment {
    	registry = "ekimkuznetsov/petclinic"
    	registryCredential = 'dockerhub-petclinic'
    }
    stages {
    	stage('Checkout') {
      	    steps {
            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs:     [[credentialsId: 'petclinic', url: 'https://github.com/Ekimkuznetsov/spring-petclinic.git']])
            }
        }
        stage('Build') {
            steps {
                sh './mvnw package'   
            }
        }
        stage('Create Artifact') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy Image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push("${env.BUILD_NUMBER}")
                    dockerImage.push("latest")
                    }
                }
            }
        }
    }
}

