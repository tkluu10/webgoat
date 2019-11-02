pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile 'KubernetesPod.yml'
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
                    script {
                        app = docker.build("tkluu10/webgoat", "./webgoat-server")
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