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
                    def testStatus = bat(script: 'npm test > test_log.txt 2>&1', returnStatus: true)
                    def result = testStatus == 0 ? 'SUCCESS' : 'FAILURE'
                    writeFile file: 'test_log.txt', text: "Run Tests Stage: ${result}\n\n" + readFile('test_log.txt')
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
                    def auditStatus = bat(script: 'npm audit > audit_log.txt 2>&1', returnStatus: true)
                    def result = auditStatus == 0 ? 'SUCCESS' : 'FAILURE'
                    writeFile file: 'audit_log.txt', text: "NPM Audit Stage: ${result}\n\n" + readFile('audit_log.txt')
                }
            }
        }

        stage('Email Notification') {
            steps {
                emailext(
                    to: 'raminsenmitha@gmail.com',
                    subject: 'Build Stage Logs and Status',
                    body: 'Attached are the logs for Run Tests and NPM Audit stages with their status.',
                    attachmentsPattern: 'test_log.txt,audit_log.txt'
                )
            }
        }
    }
}
