pipeline {
    agent { label 'custom-docker-agent' }

    environment {
        DOCKER_IMAGE = "devpremoli/flask-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devpremoli/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d --name flask-container -p 8081:8081 $DOCKER_IMAGE'
                sleep 10  // Wait for the container to start
            }
        }

        stage('Test Application') {
            steps {
                sh 'curl -f http://localhost:8081 || (docker logs flask-container && exit 1)'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker rm -f flask-container || true'
                sh 'docker rmi -f $DOCKER_IMAGE || true'
            }
        }
    }
}
