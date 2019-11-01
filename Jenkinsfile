pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yaml """
            apiVersion: v1
            kind: Pod
            metadata:
            labels:
                jenkins: slave
            spec:
            containers:
            - name: maven
                image: maven:3.6.2-jdk-11-slim
                command:
                - cat
                tty: true
            - name: docker
                image: docker:latest
                command:
                - cat
                tty: true
            """
        }
    }
    stages {
        stage('Build Maven Project') {
            steps {
                container('maven') {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    script {
                        sh 'cd webgoat-server'
                        app = docker.build("tkluu10/webgoat")
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                            app.push("${env.BUILD_NUMBER}")
                            app.push("latest")    
                        }
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