pipeline {
    agent any

    stages {
        stage('Checkout Source') {
            steps{
                git url: 'https://github.com/viniciuscaol/rotten-potatoes.git', branch:'main'
            }
        }

        stage('Build Image') {
            steps{
               script {
                    dockerapp = docker.build("viniciuscaol/rotten-potatoes:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
               }
            }
        }

        stage('Push Image') {
            steps{
               script {
                    docker.withRegistry('https://registry.hub.docker.com', "dockerhub") {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
               }
            }
        }

        stage('Deploy Kubernetes'){
            steps{
                withKubeconfig ([credentialslsId: 'kubeconfig']){
                    sh 'kubectl apply -f ./k8s -r'
                }
            }
        }
    }
}