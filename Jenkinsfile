pipeline {
    agent any
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
     environment {
        registry = "604568061403.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/imkiran13/docker-spring-boot-app.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
         stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 604568061403.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push 604568061403.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo:$BUILD_NUMBER"
                    
                }
            }
        }
        stage ("Helm package") {
            steps {
                    sh "helm package springboot"
                }
            }
                
        stage ("Helm install") {
            steps {
                    sh "helm upgrade myrelease-21 springboot-0.1.0.tgz"
                }
            }
        
        
    }
}
