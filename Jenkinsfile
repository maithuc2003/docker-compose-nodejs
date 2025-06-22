pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "maithuc2003/user_service:latest"
  }

  stages {
    stage('Clone repository') {
      steps {
        echo '✅ Repo cloned automatically by Jenkins'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          bat "docker build -t %DOCKER_IMAGE% ."
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'DOCKERHUB', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          bat '''
            echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
            docker push %DOCKER_IMAGE%
          '''
        }
      }
    }

    stage('Run with Docker Compose') {
      steps {
        configFileProvider([configFile(fileId: 'env_user_service', targetLocation: '.env')]) {
          bat '''
            docker-compose --env-file .env pull
            docker-compose --env-file .env up -d --remove-orphans
          '''
        }
      }
    }
  }

  post {
    success {
      echo '✅ Deployment successful!'
    }
    failure {
      echo '❌ Something went wrong!'
    }
  }
}
