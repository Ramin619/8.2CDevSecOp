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
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        bat 'npm test > test_log.txt 2>&1'
                    }
                }
            }
            post {
                success {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Run Tests: SUCCESS',
                        body: 'The Run Tests stage completed successfully.',
                        attachmentsPattern: 'test_log.txt'
                    )
                }
                failure {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Run Tests: FAILURE',
                        body: 'The Run Tests stage failed. See attached log.',
                        attachmentsPattern: 'test_log.txt'
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
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        bat 'npm audit > audit_log.txt 2>&1'
                    }
                }
            }
            post {
                success {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Security Scan: SUCCESS',
                        body: 'NPM Audit completed successfully.',
                        attachmentsPattern: 'audit_log.txt'
                    )
                }
                failure {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: 'Security Scan: FAILURE',
                        body: 'NPM Audit failed. See attached log.',
                        attachmentsPattern: 'audit_log.txt'
                    )
                }
            }
        }

        stage('Email Notification') {
            steps {
                echo "Pipeline completed."
            }
            post {
                success {
                    mail to: 'raminsenmitha@gmail.com',
                         subject: 'Build Status: SUCCESS',
                         body: 'The build pipeline finished successfully.'
                }
                failure {
                    mail to: 'raminsenmitha@gmail.com',
                         subject: 'Build Status: FAILURE',
                         body: 'The build pipeline failed.'
                }
            }
        }
    }
}
