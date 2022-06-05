def app

pipeline {
    agent any

    stages {
        stage('Build image') {
            steps {
                app = docker.build('suphakrit/my-todoapp')
            }
        }
        stage('Push image to registry') {
            steps {
                docker.withRegistry('https://hub.docker.com', 'docker-key') {
                    app.push('suphakrit/my-todoapp:latest')
                }
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['prod-credential']) {
                    sh 'scp -o StrictHostKeyChecking=no docker-compose.yml ubuntu-22-04@192.168.64.5:/home/ubuntu-22-04/docker-compose.yml'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu-22-04@192.168.64.5 bash /home/ubuntu-22-04/docker-compose.yml down'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu-22-04@192.168.64.5 bash /home/ubuntu-22-04/docker-compose.yml up -d'
                }
            }
        }
    }
}
