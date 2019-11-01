pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                def mvnHome = tool name: 'maven', type: 'maven'
                echo "Running Build Automation"
                sh "${mvnHome}/bin/mvn package"
            }
        }
        stage('Test') { 
            steps {
                echo "Executing Unit Tests" 
            }
        }
        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image" 
                    }
                }
        stage('Push Docker Image'){
            steps {
                echo "Pushing Docker Image"               
            }
        }
        stage('Production') {
            steps {
                echo "Deploying to Production Server"
            }
        }
    }
}