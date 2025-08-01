pipeline {
    agent any

    environment {
        EC2_USER = "ubuntu"
        EC2_HOST = "16.176.225.235"
        APP_DIR = "/var/www/html/Frontend-"
        PRIVATE_KEY = credentials('jenkins-ec2-key') // Jenkins credential ID
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['jenkins-ec2-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_HOST '
                        mkdir -p $APP_DIR &&
                        rm -rf $APP_DIR/*'
                    scp -r dist/* $EC2_USER@$EC2_HOST:$APP_DIR
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
