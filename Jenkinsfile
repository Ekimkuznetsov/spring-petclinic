pipeline {
    agent any
    
    options{
        buildDiscarder(logRotator(numToKeepStr: '5', daysToKeepStr: '5'))
        timestamps()
    }
    
    environment {
        APP = "petclinic"
        PROJECT_ID = 'qwiklabs-gcp-02-3798004c6b7b'
        CLUSTER_NAME = 'tf-gke-k8s'
        LOCATION = 'us-west1-b'
        CREDENTIALS_ID = 'kubernetes'
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
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'USERPASS')]) {
            	        sh "docker login -u ekimkuznetsov -p ${USERPASS}"
	            }
	                dockerImage.push("${env.BUILD_ID}")
                    
                }
            }
        }
        
        stage('Deploy to K8s') {
	    steps{
		echo "Deployment started ..."
	        sh 'ls -ltr'
		sh 'pwd'
		sh "sed -i 's/tagversion/${env.BUILD_ID}/g' petclinic-deployment.yaml"
		echo "Starting deployment of petclinic-deployment.yaml"
		step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'petclinic-deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		echo "Deployment Finished ..."
            }
	}
    }
}

