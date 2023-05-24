pipeline {
    agent any
    tools {
        maven "Maven"
    }
    environment {
        docker_repo = "358966077154.dkr.ecr.us-east-1.amazonaws.com/springboot-app"
    }
    stages {
        stage('Git Clone') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/WillKay28/Microsvc-springboot-app-EKS-Jenkins-ECR-Docker']])
            }
        }
        
        stage('Build jar file') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build docker_repo
                }
            }
        }
        
        stage('Push Image to ECR') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 358966077154.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 358966077154.dkr.ecr.us-east-1.amazonaws.com/springboot-app:latest'
            }
        }
        
        stage('Deploy to K8s') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                        sh 'kubectl apply -f springbootApp-eks-deploy-k8s.yaml'
                    }
                }
            }
        }
    }
}

