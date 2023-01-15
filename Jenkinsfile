pipeline {
    tools { 
        maven "Maven3"
    }
    
    environment {
        registry = "353258999787.dkr.ecr.us-east-1.amazonaws.com/my-demo-repo"
    }
    
    agent any
  
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anilreddy7394/springboot-app']])
            }
        }
        
        stage('Build Jar') {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage('Build image') {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage('Push into ECR') {
            steps {
                sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 353258999787.dkr.ecr.us-east-1.amazonaws.com"
                sh "docker push 353258999787.dkr.ecr.us-east-1.amazonaws.com/my-demo-repo:latest"
            }
        }
        
        stage('K8S Deploy') {
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                sh "kubectl apply -f eks-deploy-k8s.yaml"
            }
          }
       }
    }
}
