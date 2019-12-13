pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile 'KubernetesPod.yml'
        }
    }
    stages {
        stage('Maven Build & Sonar Scan') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    container('maven') {
                        sh 'mvn -DskipTests package sonar:sonar org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom'
                    }
                }
            }
        }
        stage('SonarQube Quality Gate'){
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('OWASP Dependency-Track') {
            steps {
                dependencyTrackPublisher artifact: './target/bom.xml', 
                artifactType: 'bom',
                projectId: 'b7595fff-0d27-40d7-bd25-1198bdd01a4d',
                synchronous: true,
                failedNewCritical: 1,
                failedNewHigh: 1,
                failedNewLow: 10,
                failedNewMedium: 5,
                failedTotalCritical: 1,
                failedTotalHigh: 5,
                failedTotalLow: 20,
                failedTotalMedium:10,
                unstableNewCritical: 1,
                unstableNewHigh: 1,
                unstableNewLow: 10,
                unstableNewMedium: 5,
                unstableTotalCritical: 1,
                unstableTotalHigh: 5,
                unstableTotalLow: 20,
                unstableTotalMedium: 10
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
        stage('Deploy to Staging Environment') {
            steps {
                echo "Deploying to Staging Server"
                sshagent(credentials : ['webgoat-staging']) {
                    sh 'ssh -o StrictHostKeyChecking=no ec2-user@$webgoat_staging_ip ./deploy.sh'
                }
            }
        }
    }
}
