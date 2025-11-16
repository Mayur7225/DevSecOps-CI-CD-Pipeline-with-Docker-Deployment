pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Mayur7225/node-todo-cicd.git"
        BRANCH   = "main"
        IMAGE    = "mayur7225/node-todo:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning clean repo..."
                sh '''
                    rm -rf source
                    mkdir source
                    cd source
                    git clone -b ${BRANCH} ${REPO_URL} .
                '''
            }
        }

        stage('Install Node Modules') {
            steps {
                echo "Running npm install..."
                sh '''
                    cd source
                    docker run --rm \
                      -v "$PWD":/app \
                      -w /app \
                      node:18-alpine \
                      sh -c "npm install"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image"
                sh '''
                    cd source
                    docker build -t ${IMAGE} .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing Docker image"
                sh '''
                    docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
                    docker push ${IMAGE}
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying with docker compose"
                sh '''
                    cd source
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }
    }

    post {
        failure {
            echo "PIPELINE FAILED ❌"
        }
        success {
            echo "PIPELINE SUCCESSFUL ✅"
        }
    }
}
