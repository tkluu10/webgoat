pipeline {
    agent {
        docker {
            image 'maven:3.6.2-jdk-11-slim' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                echo "Running Build Automation"
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test') { 
            steps {
                echo "Executing Unit Tests" 
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build("webgoat/webgoat-server")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push Docker Image'){
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Production') {
            when {
                branch 'master'
            }
            steps {
                input 'Approve to deploy to production?'
                milestone(1)
                echo "Deploying to Production Server"
            }
        }
    }
}