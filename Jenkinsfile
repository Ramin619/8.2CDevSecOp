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
                bat 'npm test > email_noti.txt 2>&1'
            }
            post {
                success {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Run Tests: SUCCESS',
                        body: '''The "Run Tests" stage completed successfully.

Please find the log attached.''',
                        attachmentsPattern: 'email_noti.txt'
                    )
                }
                failure {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Run Tests: FAILURE',
                        body: '''The "Run Tests" stage failed.

Please find the log attached for details.''',
                        attachmentsPattern: 'email_noti.txt'
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
                bat 'npm audit > email_noti.txt 2>&1'
            }
            post {
                success {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Security Scan: SUCCESS',
                        body: '''The "Security Scan" stage completed successfully.

Please find the log attached.''',
                        attachmentsPattern: 'email_noti.txt'
                    )
                }
                failure {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Security Scan: FAILURE',
                        body: '''The "Security Scan" stage failed.

Please find the log attached for vulnerability details.''',
                        attachmentsPattern: 'email_noti.txt'
                    )
                }
            }
        }
    }
}
