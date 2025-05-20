pipeline {
    agent any

    environment {
        LOG_DIR = "${WORKSPACE}\\logs"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    bat "mkdir ${LOG_DIR}"
                    bat """
                    echo Starting Checkout... > ${LOG_DIR}\\checkout_log.txt
                    git clone https://github.com/Ramin619/8.2CDevSecOp.git >> ${LOG_DIR}\\checkout_log.txt 2>>&1
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    bat """
                    echo Installing dependencies... > ${LOG_DIR}\\install_log.txt
                    npm install >> ${LOG_DIR}\\install_log.txt 2>>&1
                    """
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    try {
                        bat """
                        echo Running tests... > ${LOG_DIR}\\test_log.txt
                        npm test >> ${LOG_DIR}\\test_log.txt 2>>&1
                        """
                        currentBuild.result = 'SUCCESS'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
            post {
                always {
                    emailext (
                        subject: "Test Stage Result: ${currentBuild.result}",
                        body: "The test stage has completed with status: ${currentBuild.result}",
                        to: 'raminsenmitha@gmail.com',
                        attachmentsPattern: 'logs/checkout_log.txt, logs/install_log.txt, logs/test_log.txt'
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat """
                echo Generating coverage report... > ${LOG_DIR}\\coverage_log.txt
                npm run coverage >> ${LOG_DIR}\\coverage_log.txt 2>>&1
                """
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    try {
                        bat """
                        echo Running NPM audit... > ${LOG_DIR}\\security_log.txt
                        npm audit >> ${LOG_DIR}\\security_log.txt 2>>&1
                        """
                        currentBuild.result = 'SUCCESS'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
            post {
                always {
                    emailext (
                        subject: "Security Scan Result: ${currentBuild.result}",
                        body: "Security scan completed with status: ${currentBuild.result}",
                        to: 'raminsenmitha@gmail.com',
                        attachmentsPattern: 'logs/security_log.txt, logs/coverage_log.txt'
                    )
                }
            }
        }
    }
}

