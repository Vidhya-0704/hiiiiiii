pipeline {
    agent any

    environment {
        REPO = 'https://github.com/Become-DevOps/proj.git'
        IMAGE = 'my-html-site'
        PORT = '8080'
    }

    stages {
        stage('Clone Project') {
            steps {
                git branch: 'main', url: "${REPO}"
            }
        }

        stage('Install Docker on QA Node') {
            steps {
                ansiblePlaybook(
                    inventory: '/etc/ansible/hosts',
                    playbook: 'install_tools.yml'
                )
            }
        }

        stage('Build Docker Image on QA') {
            steps {
                sh '''
                ssh sree@172.31.92.255 "cd ~/proj && docker build -t $IMAGE ."
                '''
            }
        }

        stage('Run Docker Container on QA') {
            steps {
                sh '''
                ssh sree@172.31.92.255 "docker rm -f $IMAGE || true && docker run -d -p $PORT:80 --name $IMAGE $IMAGE"
                '''
            }
        }
    }
}