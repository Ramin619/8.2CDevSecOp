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
                    writeFile file: 'email_noti_tests.txt', text: "Run Tests Stage: ${testStatus == 0 ? 'SUCCESS' : 'FAILURE'}\n" + readFile('email_noti_tests.txt')
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
                    writeFile file: 'email_noti_audit.txt', text: "NPM Audit Stage: ${auditStatus == 0 ? 'SUCCESS' : 'FAILURE'}\n" + readFile('email_noti_audit.txt')
                }
            }
        }

        stage('Email Notification') {
            steps {
                emailext(
                    to: 'raminsenmitha@gmail.com',
                    subject: 'Build Status Email',
                    body: 'Please find the status of the stages attached.',
                    attachmentsPattern: 'email_noti_tests.txt,email_noti_audit.txt'
                )
            }
        }
    }
}
