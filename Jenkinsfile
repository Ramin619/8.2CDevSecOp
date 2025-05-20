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
          withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
            try {
              bat """
              echo. >> "${LOG_PATH}"
              echo ======= Run Tests ======= >> "${LOG_PATH}"
              snyk config set api=%SNYK_TOKEN% >> "${LOG_PATH}" 2>>&1
              npm test >> "${LOG_PATH}" 2>>&1
              echo [SUCCESS] Tests execution >> "${LOG_PATH}"
              """
            } catch (e) {
              bat """
              echo [FAILURE] Tests execution >> "${LOG_PATH}"
              """
              error("Tests failed")
            }
          }
        }
      }
      post {
        always {
          bat "copy \"${LOG_PATH}\" pipeline_log.txt"
          emailext(
            subject: "Test Stage Result: ${currentBuild.result}",
            body: "The test stage completed with status: ${currentBuild.result}",
            to: "raminsenmitha@gmail.com",
            attachmentsPattern: "pipeline_log.txt"
          )
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
            try {
              bat """
              echo. >> "${LOG_PATH}"
              echo ======= Security Scan ======= >> "${LOG_PATH}"
              snyk test >> "${LOG_PATH}" 2>>&1
              echo [SUCCESS] Security scan completed >> "${LOG_PATH}"
              """
            } catch (e) {
              bat """
              echo [FAILURE] Security scan >> "${LOG_PATH}"
              """
              error("Security scan failed")
            }
          }
        }
      }
      post {
        always {
          bat "copy \"${LOG_PATH}\" pipeline_log.txt"
          emailext(
            subject: "Security Scan Result: ${currentBuild.result}",
            body: "The security scan stage completed with status: ${currentBuild.result}",
            to: "raminsenmitha@gmail.com",
            attachmentsPattern: "pipeline_log.txt"
          )
        }
      }
    }
  }
}
