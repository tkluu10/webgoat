pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                echo "Running Build Automation"
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