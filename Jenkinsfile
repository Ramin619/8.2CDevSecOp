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
                    def status = catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE', returnStatus: true) {
                        bat 'npm test > email_noti.txt 2>&1'
                    }
                    currentBuild.description = "Run Tests Status: ${status == 0 ? 'SUCCESS' : 'FAILURE'}"
                }
            }
            post {
                always {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: "Run Tests Stage Result: ${currentBuild.description}",
                        body: '''The "Run Tests" stage has completed.

Check the attached log for details.''',
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
                script {
                    def auditStatus = catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE', returnStatus: true) {
                        bat 'npm audit > email_noti.txt 2>&1'
                    }
                    currentBuild.description = "Audit Status: ${auditStatus == 0 ? 'SUCCESS' : 'FAILURE'}"
                }
            }
            post {
                always {
                    emailext(
                        to: 'raminsenmitha@gmail.com',
                        subject: "Security Scan Stage Result: ${currentBuild.description}",
                        body: '''The "Security Scan" stage has completed.

Check the attached log for vulnerability results.''',
                        attachmentsPattern: 'email_noti.txt'
                    )
                }
            }
        }
    }
}
