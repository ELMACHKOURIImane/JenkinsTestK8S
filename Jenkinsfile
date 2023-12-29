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
        stage("Deploy to K8S") {
            steps {
                script {
                    def clusterName = 'myFirstCluster'
                    def contextName = 'my-context'
                    def credentialsId = 'K8S'
                    def namespace = 'my-namespace'
                    def restrictKubeConfigAccess = false
                    def serverUrl = 'https://myfirstclu-myfirstresource-de497f-up15d38c.hcp.southcentralus.azmk8s.io'
                    withKubeConfig(
                        clusterName: clusterName,
                        contextName: contextName,
                        credentialsId: credentialsId,
                        namespace: namespace,
                        restrictKubeConfigAccess: restrictKubeConfigAccess,
                        serverUrl: serverUrl
                    ) {
                        sh 'kubectl apply -f deployment.yaml'
                    }
                }
            }
        }

     }
}