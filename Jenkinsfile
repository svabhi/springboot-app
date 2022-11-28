pipeline {
    agent any
  tools {
        maven "Maven3"
        }
    environment {
        registryName = "aksabhitestes"
        registryCredential = "ACR"
        dockerImage = ""
        registryURL = "aksabhitestes.azurecr.io"
    }
    
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/svabhi/springboot-app.git']]])
            }
        }
        
        stage ('Build') {
        steps {
            sh 'mvn clean install'           
        }
     }
     
    stage ('Build Docker image') {
        steps {
                script {
                    dockerImage = docker.build registryName
                }
            }
        }
        
    // Uploading Docker images into ACR
        stage('Upload Image to ACR') {
         steps{   
             script {
                docker.withRegistry( "http://${registryUrl}", registryCredential ) {
                dockerImage.push()
                }
            }
          }
        }
        
        stage ('K8S Deploy') {
          steps {
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: 'https://aks-abhi-test-es-dns-12d44ec5.hcp.eastus.azmk8s.io']) {
                sh ('kubectl apply -f jenkins-aks-deploy-from-acr.yaml')
                }
            }
        }
     }
    }
}