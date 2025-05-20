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
                    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                        // Authenticate snyk
                        bat 'snyk config set api=%SNYK_TOKEN%'
                        // Run tests
                        def testStatus = bat(script: 'npm test > email_noti_tests.txt 2>&1', returnStatus: true)
                        def result = testStatus == 0 ? "SUCCESS" : "FAILURE"
                        emailext(
                            to: 'raminsenmitha@gmail.com',
                            subject: "Test Stage Result: ${result}",
                            body: "The 'Run Tests' stage has completed with status: ${result}.\nCheck the attached test log.",
                            attachmentsPattern: 'email_noti_tests.txt'
                        )
                    }
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
                    def result = auditStatus == 0 ? "SUCCESS" : "FAILURE"
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: "Security Scan Stage Result: Audit Status: ${result}",
                        body: "The 'Security Scan' stage has completed with status: ${result}.\nCheck the attached audit log.",
                        attachmentsPattern: 'email_noti_audit.txt'
                    )
                }
            }
        }
    }
}
