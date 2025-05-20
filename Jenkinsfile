pipeline {
  agent any

  environment {
    LOG_DIR = "E:\\Deakin University\\T1 S1\\SIT753 - Professional Practice in Information Technology\\8.1C - Continuous Integration and DevSecOps in with Jenkins"
  }

  stages {
    stage('Checkout') {
      steps {
        script {
          bat """
          if not exist "${LOG_DIR}" (
            mkdir "${LOG_DIR}"
          )
          echo Starting Checkout... > "${LOG_DIR}\\checkout_log.txt"
          echo Repository was already checked out by Jenkins >> "${LOG_DIR}\\checkout_log.txt"
          """
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        script {
          bat """
          echo Installing dependencies... > "${LOG_DIR}\\install_log.txt"
          npm install >> "${LOG_DIR}\\install_log.txt" 2>>&1
          """
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          try {
            bat """
            echo Running tests... > "${LOG_DIR}\\test_log.txt"
            npm test >> "${LOG_DIR}\\test_log.txt" 2>>&1

            REM Copy logs into Jenkins workspace for email attachment
            copy "${LOG_DIR}\\checkout_log.txt" "checkout_log.txt"
            copy "${LOG_DIR}\\install_log.txt" "install_log.txt"
            copy "${LOG_DIR}\\test_log.txt" "test_log.txt"
            """
            currentBuild.result = 'SUCCESS'
          } catch (e) {
            currentBuild.result = 'FAILURE'
            throw e
          }
        }
      }
      post {
        always {
          emailext(
            subject: "Test Stage Result: ${currentBuild.result}",
            body:    "The test stage has completed with status: ${currentBuild.result}",
            to:      'raminsenmitha@gmail.com',
            attachmentsPattern: 'checkout_log.txt,install_log.txt,test_log.txt'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat """
        echo Generating coverage report... > "${LOG_DIR}\\coverage_log.txt"
        npm run coverage >> "${LOG_DIR}\\coverage_log.txt" 2>>&1
        """
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          try {
            bat """
            echo Running NPM audit... > "${LOG_DIR}\\security_log.txt"
            npm audit >> "${LOG_DIR}\\security_log.txt" 2>>&1

            REM Copy remaining logs for attachment
            copy "${LOG_DIR}\\coverage_log.txt" "coverage_log.txt"
            copy "${LOG_DIR}\\security_log.txt" "security_log.txt"
            """
            currentBuild.result = 'SUCCESS'
          } catch (e) {
            currentBuild.result = 'FAILURE'
            throw e
          }
        }
      }
      post {
        always {
          emailext(
            subject: "Security Scan Result: ${currentBuild.result}",
            body:    "Security scan completed with status: ${currentBuild.result}",
            to:      'raminsenmitha@gmail.com',
            attachmentsPattern: 'coverage_log.txt,security_log.txt'
          )
        }
      }
    }
  }
}

