pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'jdk11'
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
                    sh 'mvn package'
                }
            }
            stage('Build Docker Image') {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh "docker build -t tkluu10/webgoat:${env.BUILD_ID} ."
                        sh "docker tag tkluu10/webgoat:${env.build_ID} tkluu10/webgoat:latest"
                        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "docker push tkluu10/webgoat:${env.BUILD_ID}"
                        sh "docker push tkluu10/webgoat:latest"
                        }
                    }
                }
            stage('Push Docker Image') {
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
