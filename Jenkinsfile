pipeline {
    agent any

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

        stage('Build Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build registryName
                }
            }
        }

        stage('Push to ACR') {
            steps {
                script {
                    withDockerRegistry([credentialsId: registryCredential, url: "http://${registryUrl}"]) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage("Deploy to K8S") {
            steps {
                script {
                    withKubeConfig(
                        caCertificate: '',
                        clusterName: '',
                        contextName: '',
                        credentialsId: 'K8S',
                        namespace: '',
                        restrictKubeConfigAccess: false,
                        serverUrl: ''
                    ) {
                        sh 'kubectl apply -f deployment.yaml'
                    }
                }
            }
        }
    }
}
