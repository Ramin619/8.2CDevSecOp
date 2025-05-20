pipeline {
  agent any

  environment {
    LOG_DIR = "E:\\Deakin University\\T1 S1\\SIT753 - Professional Practice in Information Technology\\8.1C - Continuous Integration and DevSecOps in with Jenkins"
    LOG_FILE = "${LOG_DIR}\\pipeline_log.txt"
  }

  stages {
    stage('Checkout') {
      steps {
        script {
          bat """
          if not exist "${LOG_DIR}" (
            mkdir "${LOG_DIR}"
          )
          echo ======= Checkout Stage ======= > "${LOG_FILE}"
          echo Repository was already checked out by Jenkins >> "${LOG_FILE}"
          echo [SUCCESS] Checkout completed >> "${LOG_FILE}"
          """
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        script {
          def result = bat(script: """
            echo. >> "${LOG_FILE}"
            echo ======= Install Dependencies ======= >> "${LOG_FILE}"
            npm install >> "${LOG_FILE}" 2>>&1
          """, returnStatus: true)

          bat """
            echo [${result == 0 ? 'SUCCESS' : 'FAILURE'}] Dependency installation >> "${LOG_FILE}"
          """

          if (result != 0) {
            error("Install Dependencies failed")
          }
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          def result = bat(script: """
            echo. >> "${LOG_FILE}"
            echo ======= Run Tests ======= >> "${LOG_FILE}"
            npm test >> "${LOG_FILE}" 2>>&1
          """, returnStatus: true)

          bat """
            echo [${result == 0 ? 'SUCCESS' : 'FAILURE'}] Tests execution >> "${LOG_FILE}"
            copy "${LOG_FILE}" "pipeline_log.txt"
          """

          emailext(
            subject: "Run Tests Result: ${result == 0 ? 'SUCCESS' : 'FAILURE'}",
            body:    "The 'Run Tests' stage completed with status: ${result == 0 ? 'SUCCESS' : 'FAILURE'}",
            to:      'raminsenmitha@gmail.com',
            attachmentsPattern: 'pipeline_log.txt'
          )

          if (result != 0) {
            error("Tests failed")
          }
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        script {
          def result = bat(script: """
            echo. >> "${LOG_FILE}"
            echo ======= Generate Coverage Report ======= >> "${LOG_FILE}"
            npm run coverage >> "${LOG_FILE}" 2>>&1
          """, returnStatus: true)

          bat """
            echo [${result == 0 ? 'SUCCESS' : 'FAILURE'}] Coverage generation >> "${LOG_FILE}"
          """

          if (result != 0) {
            error("Coverage report failed")
          }
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          def result = bat(script: """
            echo. >> "${LOG_FILE}"
            echo ======= NPM Audit (Security Scan) ======= >> "${LOG_FILE}"
            npm audit >> "${LOG_FILE}" 2>>&1
          """, returnStatus: true)

          bat """
            echo [${result == 0 ? 'SUCCESS' : 'FAILURE'}] Security scan >> "${LOG_FILE}"
            copy "${LOG_FILE}" "pipeline_log.txt"
          """

          emailext(
            subject: "Security Scan Result: ${result == 0 ? 'SUCCESS' : 'FAILURE'}",
            body:    "The 'Security Scan' stage completed with status: ${result == 0 ? 'SUCCESS' : 'FAILURE'}",
            to:      'raminsenmitha@gmail.com',
            attachmentsPattern: 'pipeline_log.txt'
          )

          if (result != 0) {
            error("Security scan failed")
          }
        }
      }
    }
  }
}
