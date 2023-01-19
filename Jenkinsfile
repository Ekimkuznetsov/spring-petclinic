pipeline {
    agent any
    
    options{
        buildDiscarder(logRotator(numToKeepStr: '5', daysToKeepStr: '5'))
        timestamps()
    }
    
    environment {
        APP = "petclinic"
        PROJECT_ID = 'qwiklabs-gcp-02-f66c30123261'
        CLUSTER_NAME = 'scaling-demo'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'qwiklabs-gcp-02-f66c30123261'
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
                    dockerImage = docker.build("ekimkuznetsov/petclinic:${env.BUILD_ID}") 
                }
            }
        }
        
        stage('Push Image') {
            steps{
                script {
                    echo "Pushing Docker Image"
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerID') {
                            dockerImage.push("latest")
                            dockerImage.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        

	stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/tagversion:latest/tagversion:${env.BUILD_ID}/g' petclinic-deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}

