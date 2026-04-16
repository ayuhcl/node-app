pipeline {
  agent any

  tools {
    nodejs 'node18'
  }

  environment {
    SONAR_TOKEN = credentials('sonarqube-token')
  }

  stages {

    stage('Clone Code') {
      steps {
        git:'https://github.com/ayuhcl/node-app.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarServer') {
          sh """
          sonar-scanner \
          -Dsonar.login=$SONAR_TOKEN
          """
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t node-app .'
      }
    }

    stage('Run Container') {
      steps {
        sh '''
        docker stop nodeapp || true
        docker rm nodeapp || true
        docker run -d -p 3000:3000 --name nodeapp node-app
        '''
      }
    }
  }
}