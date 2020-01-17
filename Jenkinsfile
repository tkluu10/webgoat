pipeline {
    environment {
        registry_credential = 'docker' // Credentials to DockerHub account stored within Jenkins
        staging_credential = 'webgoat-staging' // Credentials for staging server stored within Jenkins
        staging_ip = "3.231.219.37" // IP address of staging server removed for security purposes
        staging_user = "ec2-user" // Username of staging server hosted on AWS EC2 instance
    }
    agent {
        openshift {
            defaultContainer 'jnlp'
            yamlFile 'KubernetesPod.yml'
        }
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/tkluu10/webgoat.git'
            }
        }
        stage('SAST') {
            steps {
                echo 'Performing static code analysis with SonarQube...'
                echo 'Generating list of dependencies...'
                withSonarQubeEnv('sonarqube') {
                    container('maven') {
                        sh 'mvn -DskipTests package sonar:sonar org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom'
                    }
                }
            }
        }
        stage('Quality Gate'){
            steps {
                echo 'Waiting for Quality Gate results...'
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Publish to Dependency Track') {
            steps {
                echo 'Publishing list of dependencies to Dependency Track...'
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
                echo 'Building application into Docker image...'
                container('docker') {
                    script {
                        webgoat = docker.build("tkluu10/webgoat", "./webgoat-server")
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                container('docker') {
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', registry_credential) {
                            webgoat.push("${env.BUILD_NUMBER}")
                            webgoat.push("latest") 
                        }
                    }
                }      
            }
        }
        stage('Deploy to Staging Server') {
            steps {
                echo "Deploying to staging server..."
                sshagent(credentials : [staging_credential]) {
                    sh 'ssh -o StrictHostKeyChecking=no ${staging_user}@${staging_ip} ./deploy.sh'
                }
            }
        }
        stage('DAST') {
            steps {
                echo "Running ZAP baseline scan..."
                container('zap') {
                    script {
                        sh './zap-baseline.py -t http://${staging_ip}:8080 -r report.html || true'
                    }
                }
            }
        }
    }
}
