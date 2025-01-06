pipeline {
    agent any
    environment {
        IMAGE_NAME = 'serikzhanhc/mybuildimage'
        IMAGE_TAG = '1.0.0'
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [[$class: 'CloneOption', shallow: true, depth: 1]],
                    userRemoteConfigs: [[
                        url: 'https://github.com/serikzhanup/cicd-pipeline',
                        credentialsId: 'github_pat'
                    ]]
                ])
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building the application...'
                sh '''
                    chmod +x scripts/build.sh
                    ./scripts/build.sh
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                sh '''
                    chmod +x scripts/test.sh
                    ./scripts/test.sh
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
    post {
        always {
            echo 'Cleaning up unused Docker resources...'
            sh 'docker system prune -f || true'
        }
    }
}
