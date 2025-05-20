pipeline {
    agent any

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
                script {
                    def testStatus = bat(script: 'npm test > email_noti_tests.txt 2>&1', returnStatus: true)
                    currentBuild.description = "Test Status: ${testStatus == 0 ? 'SUCCESS' : 'FAILURE'}"
                }
            }
            post {
                always {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: "Test Stage Result: ${currentBuild.description}",
                        body: 'The "Run Tests" stage has completed.\n\nCheck the attached log for test results.',
                        attachmentsPattern: 'email_noti_tests.txt'
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    def auditStatus = bat(script: 'npm audit > email_noti_audit.txt 2>&1', returnStatus: true)
                    currentBuild.description = "Audit Status: ${auditStatus == 0 ? 'SUCCESS' : 'FAILURE'}"
                }
            }
            post {
                always {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: "Security Scan Stage Result: ${currentBuild.description}",
                        body: 'The "Security Scan" stage has completed.\n\nCheck the attached log for vulnerability results.',
                        attachmentsPattern: 'email_noti_audit.txt'
                    )
                }
            }
        }
    }
}
