pipeline {
    agent any

    stages {

        stage ('Build Docker Image') {
            steps {
                script {
                    dockerapp = docker.build("tiagosarri/kube-news:${env.BUILD_ID}", '-f ./src/dockerfile ./src')
                }
            }
        }

        stage ('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub')
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                }
            }
        }

        stage ('Deploy Kubernetes') {
            environmet {
                tag_version = "${env.BUIL_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }

    }
}