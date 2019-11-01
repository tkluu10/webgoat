pipeline {
    agent any
    tools {
            maven 'maven'
            jdk 'jdk8'
        }
        stages {
            stage('Initialize') {
                steps {
                    sh '''
                        echo "PATH = ${PATH}"
                        echo "M2_HOME = ${M2_HOME}"
                        '''
                }
            }
            stage('Build') { 
                steps {
                    sh 'mvn -Dmaven.test.failure.ignore=true install'
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
