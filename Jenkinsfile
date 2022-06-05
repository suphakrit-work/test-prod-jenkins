pipeline {
    agent any
    def app

    stages {
        stage('Build image') {
            app = docker.build('suphakrit/my-todoapp')
        }
        stage('Push image to registry') {
            docker.withRegistry('https://hub.docker.com', 'docker-key') {
                app.push('latest')
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
