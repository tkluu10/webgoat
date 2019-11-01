pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'jdk11'
        org.jenkinsci.plugins.docker.commons.tools.DockerTool 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
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
            stage('Production') {
                steps {
                    echo "Deploying to Production Server"
                }
            }
        }
    }
