pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile 'KubernetesPod.yaml'
        }
    }
    stages {
        stage('Maven Build') {
            steps {
                container('maven') {
                sh 'mvn -DskipTests clean package'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh 'cd webgoat-server'
                        script {
                            app = docker.build("tkluu10/webgoat")
                        }
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                        script {
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