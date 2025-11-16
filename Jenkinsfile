pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Mayur7225/node-todo-cicd.git"
        BRANCH   = "main"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning repo into DevSecops-CICD..."
                sh '''
                    rm -rf DevSecops-CICD
                    mkdir DevSecops-CICD
                    cd DevSecops-CICD
                    git clone -b ${BRANCH} ${REPO_URL} .
                '''
            }
        }

        stage('Prepare') {
            steps {
                echo "Installing node modules using Docker Node"
                sh '''
                    cd DevSecops-CICD
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
                    cd DevSecops-CICD
                    docker build -t mayur7225/node-todo:latest .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing Docker image"
                sh '''
                    docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
                    docker push mayur7225/node-todo:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying with docker compose"
                sh '''
                    cd DevSecops-CICD
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }

    }

    post {
        failure {
            echo "Pipeline FAILED ❌"
        }
        success {
            echo "Pipeline SUCCESSFUL ✅"
        }
    }
}

