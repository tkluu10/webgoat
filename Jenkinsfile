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
        stage('Build Docker Images') {
            steps {
                container('docker') {
                    script {
                        webgoat = docker.build("tkluu10/webgoat", "./webgoat-server")
                        webwolf = docker.build("tkluu10/webwolf", "./webwolf")
                    }
                }
            }
        }
        stage('Push Docker Images') {
            steps {
                container('docker') {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                            webgoat.push("${env.BUILD_NUMBER}")
                            webgoat.push("latest")
                            webwolf.push("${env.BUILD_NUMBER}")
                            webwolf.push("latest")    
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