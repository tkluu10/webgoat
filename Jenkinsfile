pipeline {
    agent {
        docker {
            image 'maven:3.6.2-jdk-11-slim'
        }
    }

        stages {
            stage('Build') { 
                steps {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
            stage('Build Docker Image') {
                steps {
                    script {
                        sh 'cd webgoat-server'
                        app = docker.build("tkluu10/webgoat")
                    }
                }
            }
            stage('Push Docker Image') {
                steps {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                            app.push("${env.BUILD_NUMBER}")
                            app.push("latest")
                        }
                    }        
                }
            }
            stage('Deploy to Production') {
                steps {
                    echo "Deploying to Production Server"
                }
            }
        }
    }
