pipeline {
    agent any
    tools {
        nodejs 'node'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/arifislam007/nodejs-cicd.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t nodejs-cicd:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Docker Build and push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'Docker-cred') {
                        sh "docker build -t arifislam/nodejs-cicd:${BUILD_NUMBER} ."
                        sh "docker push arifislam/nodejs-cicd:${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Update Deployment File Image Tag') {
            steps {
                sh "sed -i 's/nodejs-cicd:base/nodejs-cicd:${BUILD_NUMBER}/' ./deployment.yaml"
            }
        }
        stage('Deploy with Kubectl') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'kube-secret', namespace: 'java-app', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.207.197:6443') {
                    sh 'kubectl apply -f deployment.yaml -n java-app'
                }
            }
        }
    }
}
