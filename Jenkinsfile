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
                projectId: '2e110698-3e6f-4369-a481-47c922695568',
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
                    sh 'ssh -v ec2-user@$webgoat_staging_ip'
                }
            }
        }
        stage('OWASP ZAP Report') {
            steps {
                echo 'Running attack scripts...'
       //         container('zap') {
       //             script {
       //                 def retVal = sh returnStatus: true, script: './zap/zap-baseline.py -r zapreport.html -t http://3.230.142.132:8080'
       //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: './zap/wrk', reportFiles: 'zapreport.html', reportName: 'ZAP Baseline Scan', reportTitles: 'ZAP Baseline Scan'])
       //                 echo "Return value is: ${retVal}"
       //             }
       //         }
            }
        }
    }
}