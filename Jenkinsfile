pipeline {
  agent any

  tools {
    nodejs 'node25'
  }

  environment {
    SONAR_TOKEN = credentials('sonarqube-token')
  }

  stages {

    stage('Checkout') {
      steps {
        git url:'https://github.com/ayuhcl/node-app.git', branch:'main'
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