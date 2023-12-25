pipeline {
    agent any
    tools {
        maven '3.9.6'
    }
    environment {
        registryName = "insurancerepo"
        registryCredential = 'ACR'
        registryUrl = 'insurancerepo.azurecr.io'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ELMACHKOURIImane/JenkinsTestK8S']])
            }
        }
        stage('Build maven ') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('buid Image') {
            steps {
                script {
                    dockerImage = docker.build registryName
                }
            }
        }
        stage('push to ACR') {
            steps {
                script {
                    docker.withRegistry("http://${registryUrl}", registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage ("Deploy-to-K8S") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                sh "kubectl apply -f deployment.yaml"
                }
             }
         }
     }
}