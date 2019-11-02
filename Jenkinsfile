pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile 'KubernetesPod.yml'
        }
    }
    stages {
        stage('Build & Scan') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    container('maven') {
                        sh 'mvn -DskipTests package sonar:sonar org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom'
                    }
                }
            }
        }
        stage('Quality Gate'){
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Dependency-Track') {
            steps {
                dependencyTrackPublisher artifact: './target/bom.xml', 
                artifactType: 'bom',
                projectId: '2e110698-3e6f-4369-a481-47c922695568',
                synchronous: true
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    script {
                        webgoat = docker.build("tkluu10/webgoat", "./webgoat-server")
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                            webgoat.push("${env.BUILD_NUMBER}")
                            webgoat.push("latest") 
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