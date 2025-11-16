pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Mayur7225/node-todo-cicd.git"
        BRANCH = "main"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning clean repo..."
                sh '''
                    rm -rf source
                    mkdir source
                    cd source
                    git clone -b main ${REPO_URL} .
                '''
            }
        }

        stage('Prepare Node Modules') {
            steps {
                echo "Installing npm modules..."
                sh '''
                    cd source/app
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
                sh '''
                    cd source
                    docker build -t mayur7225/todo-app:latest .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh '''
                    docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
                    docker push mayur7225/todo-app:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cd source
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }
    }
}

       
      

