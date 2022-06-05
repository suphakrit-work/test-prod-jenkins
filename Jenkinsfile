pipeline {
    environment {
        destinationHost = 'ubuntu-22-04@192.168.64.5'
        destinationUserPath = '/home/ubuntu-22-04'
        sshCredential = 'prod-credential'
        registry = 'suphakrit/my-todoapp'
        registryCredential = 'docker-key'
        dockerImage = ''
    }
    agent any

    stages {
        stage('Cleaning up image dangling') {
            steps {
                sh 'docker rmi $(docker images -q -f dangling=true)'
            }
        }
        stage('Build image') {
            steps {
                script {
                    dockerImage = docker.build registry + ':latest'
                }
            }
        }
        stage('Push image to registry') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deployment') {
            steps {
                sshagent([sshCredential]) {
                    sh "scp -o StrictHostKeyChecking=no docker-compose.yml $destinationHost:$destinationUserPath/docker-compose.yml"
                    sh "ssh -o StrictHostKeyChecking=no $destinationHost pwd"
                    sh "ssh -o StrictHostKeyChecking=no $destinationHost docker-compose down"
                    sh "ssh -o StrictHostKeyChecking=no $destinationHost docker-compose up -d"
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $registry:latest"
                sh 'docker rmi $(docker images -q -f dangling=true)'
            }
        }
    }
}
