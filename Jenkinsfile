pipeline {
  agent any

  environment {
    REPO_URL = "https://github.com/Mayur7225/node-todo-cicd.git"
    BRANCH = "main"
    IMAGE = "todo-devops-app"
    DOCKERHUB_USER = "mayur7225"
    SONAR_SERVER = "Sonar"
  }

  options {
    timeout(time: 60, unit: 'MINUTES')
  }

  stages {

    stage('Prepare') {
      steps {
        echo "Installing node modules using Docker Node"
        sh '''
          docker run --rm \
            -v "$PWD":/app \
            -w /app \
            node:18-alpine \
            sh -c "npm install"
        '''
      }
    }

    stage('Dependency Scan (npm audit)') {
      steps {
        echo "Running npm audit via Docker"
        sh '''
          docker run --rm \
            -v "$PWD":/app \
            -w /app \
            node:18-alpine \
            sh -c "npm audit || true"
        '''
      }
    }

    stage('Secret Scan - Gitleaks') {
      steps {
        echo "Running Gitleaks (secret scan)"
        sh '''
          if command -v gitleaks >/dev/null 2>&1; then
            gitleaks detect --source . --exit-code 1 || true
          else
            docker run --rm -v "$PWD":/code zricethezav/gitleaks:latest \
              detect --source /code --exit-code 1 || true
          fi
        '''
      }
    }

    stage('Generate SBOM (Syft)') {
      steps {
        echo "Generating SBOM with Syft"
        sh '''
          if command -v syft >/dev/null 2>&1; then
            syft packages-dir:. -o cyclonedx-json=sbom-cyclonedx.json || true
          else
            docker run --rm -v "$PWD":/project anchore/syft:latest \
              packages dir:/project \
              -o cyclonedx-json=/project/sbom-cyclonedx.json || true
          fi
        '''
        archiveArtifacts artifacts: 'sbom-cyclonedx.json', allowEmptyArchive: true
      }
    }

    stage('SAST - SonarQube (Docker scanner)') {
      steps {
        echo "Running SonarScanner via Docker image"
        withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
          withSonarQubeEnv("${SONAR_SERVER}") {
            sh '''
              docker run --rm \
                -v "$PWD":/usr/src \
                -e SONAR_HOST_URL=$SONAR_HOST_URL \
                -e SONAR_TOKEN=$SONAR_AUTH_TOKEN \
                sonarsource/sonar-scanner-cli \
                -Dsonar.projectKey=todo-devops-app \
                -Dsonar.sources=/usr/src \
                -Dsonar.login=$SONAR_TOKEN \
                -Dsonar.host.url=$SONAR_HOST_URL
            '''
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        echo "Waiting for SonarQube Quality Gate..."
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        echo "Building Docker image"
        sh "docker build -t ${IMAGE}:latest ."
      }
    }

    stage('Container Scan - Trivy') {
      steps {
        echo "Running Trivy image scan"
        sh '''
          if command -v trivy >/dev/null 2>&1; then
            trivy image --severity HIGH,CRITICAL --exit-code 1 ${IMAGE}:latest || true
          else
            docker run --rm \
              -v /var/run/docker.sock:/var/run/docker.sock \
              aquasec/trivy:latest image --severity HIGH,CRITICAL \
              --exit-code 1 ${IMAGE}:latest || true
          fi
        '''
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHubCreds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            echo $DH_PASS | docker login -u $DH_USER --password-stdin
            docker tag ${IMAGE}:latest $DH_USER/${IMAGE}:latest
            docker push $DH_USER/${IMAGE}:latest
          '''
        }
      }
    }

    stage('Deploy (docker compose)') {
      steps {
        echo "Deploying with docker compose"
        sh '''
          docker compose down || true
          docker compose pull || true
          docker compose up -d --build
        '''
      }
    }

  } // end stages

  post {
    success {
      echo "Pipeline SUCCESS ✅"
    }
    unstable {
      echo "Pipeline UNSTABLE — Check warnings!"
    }
    failure {
      echo "Pipeline FAILED ❌ Check logs"
    }
  }
}


