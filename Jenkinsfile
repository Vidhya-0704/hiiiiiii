pipeline {
    agent any

    environment {
        REPO = 'https://github.com/Become-DevOps/proj.git'
        IMAGE = 'my-html-site'
        PORT = '8080'
        QA_HOST = '172.31.9.240' // QA private IP
        QA_USER = 'vidhya'         // QA server username
    }

    stages {
        stage('Clone Project') {
            steps {
                git branch: 'main', url: "${REPO}"
            }
        }

        stage('Copy Files to QA') {
            steps {
                sh """
                scp -r . ${QA_USER}@${QA_HOST}:/home/${QA_USER}/proj
                """
            }
        }

        stage('Build Docker Image on QA') {
            steps {
                sh """
                ssh ${QA_USER}@${QA_HOST} "cd /home/${QA_USER}/proj && docker build -t ${IMAGE} ."
                """
            }
        }

        stage('Run Docker Container on QA') {
            steps {
                sh """
                ssh ${QA_USER}@${QA_HOST} "docker rm -f ${IMAGE} || true && docker run -d -p ${PORT}:80 --name ${IMAGE} ${IMAGE}"
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit: http://${QA_HOST}:${PORT}"
        }
        failure {
            echo "❌ Deployment failed — check console output."
        }
    }
}
