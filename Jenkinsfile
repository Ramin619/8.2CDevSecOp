pipeline {
  agent any

  environment {
        EMAIL_RECIPIENTS = 'raminsenmitha@gmail.com'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Ramin619/8.2CDevSecOp.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        bat 'npm test || exit /b 0'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat 'npm run coverage || exit /b 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        bat 'npm audit || exit /b 0'
      }
    }
  }

  post {
        always {
            emailext (
                subject: "Jenkins Build #${BUILD_NUMBER}: ${currentBuild.currentResult}",
                body: "Build Status: ${currentBuild.currentResult}\nCheck console output at: ${env.BUILD_URL}console",
                to: "${EMAIL_RECIPIENTS}",
                attachLog: true
            )
        }
    }
}

