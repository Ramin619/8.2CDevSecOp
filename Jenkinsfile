pipeline {
  agent any

  environment {
    LOG_PATH = "E:\\Deakin University\\T1 S1\\SIT753 - Professional Practice in Information Technology\\8.1C - Continuous Integration and DevSecOps in with Jenkins\\pipeline_log.txt"
  }

  stages {

    stage('Checkout') {
      steps {
        script {
          bat """
          if not exist "E:\\Deakin University\\T1 S1\\SIT753 - Professional Practice in Information Technology\\8.1C - Continuous Integration and DevSecOps in with Jenkins" (
            mkdir "E:\\Deakin University\\T1 S1\\SIT753 - Professional Practice in Information Technology\\8.1C - Continuous Integration and DevSecOps in with Jenkins"
          )
          echo ======= Checkout Stage ======= > "${LOG_PATH}"
          echo Repository was already checked out by Jenkins >> "${LOG_PATH}"
          echo [SUCCESS] Checkout completed >> "${LOG_PATH}"
          """
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        script {
          bat """
          echo. >> "${LOG_PATH}"
          echo ======= Install Dependencies ======= >> "${LOG_PATH}"
          npm install >> "${LOG_PATH}" 2>>&1
          echo [SUCCESS] Dependency installation >> "${LOG_PATH}"
          """
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          try {
            withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
              bat """
              echo. >> "${LOG_PATH}"
              echo ======= Run Tests ======= >> "${LOG_PATH}"
              set SNYK_TOKEN=%SNYK_TOKEN%
              npm test >> "${LOG_PATH}" 2>>&1
              echo [SUCCESS] Tests execution >> "${LOG_PATH}"
              """
            }
          } catch (e) {
            bat "echo [FAILURE] Tests execution >> \"${LOG_PATH}\""
            error("Tests failed")
          }
        }
      }
      post {
        always {
          bat "copy \"${LOG_PATH}\" pipeline_log.txt"
          emailext(
            subject: "Test Stage Result: ${currentBuild.result}",
            body: "The test stage has completed with status: ${currentBuild.result}",
            to: 'raminsenmitha@gmail.com',
            attachmentsPattern: 'pipeline_log.txt'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat """
        echo. >> "${LOG_PATH}"
        echo ======= Generate Coverage Report ======= >> "${LOG_PATH}"
        npm run coverage >> "${LOG_PATH}" 2>>&1
        echo [SUCCESS] Coverage report generation >> "${LOG_PATH}"
        """
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          try {
            bat """
            echo. >> "${LOG_PATH}"
            echo ======= NPM Audit ======= >> "${LOG_PATH}"
            npm audit >> "${LOG_PATH}" 2>>&1
            echo [SUCCESS] NPM Audit completed >> "${LOG_PATH}"
            """
          } catch (e) {
            bat "echo [FAILURE] NPM Audit >> \"${LOG_PATH}\""
            error("Security scan failed")
          }
        }
      }
      post {
        always {
          bat "copy \"${LOG_PATH}\" pipeline_log.txt"
          emailext(
            subject: "Security Scan Result: ${currentBuild.result}",
            body: "The NPM audit stage has completed with status: ${currentBuild.result}",
            to: 'raminsenmitha@gmail.com',
            attachmentsPattern: 'pipeline_log.txt'
          )
        }
      }
    }
  }
}
