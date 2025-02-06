pipeline {
    agent { label 'custom-docker-agent' }

    environment {
        DOCKER_IMAGE = "devpremoli/flask-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker rm -f flask-container || true'
                sh 'docker run -d --name flask-container -p 8081:8081 $DOCKER_IMAGE'
                sleep 10  // Wait for the container to start
            }
        }

        stage('Test Application') {
            steps {
                script {
                    def container_ip = sh(script: "docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' flask-container", returnStdout: true).trim()
                    sh "curl -f http://${container_ip}:8081 || (docker logs flask-container && exit 1)"
                }
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
