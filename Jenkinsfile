podTemplate(containers: [
    containerTemplate(name: 'maven', image: 'maven:3.6.2-jdk-11-slim', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker:latest', ttyEnabled: true, command: 'cat')
  ]) {
    node(POD_LABEL) {
        stage('Pull from SCM') {
            git 'https://github.com/tkluu10/webgoat.git'
            container('maven') {
                stage('Maven Build') {
                    sh 'mvn -B clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh 'cd webgoat-server'
                    app = docker.build("tkluu10/webgoat")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")    
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