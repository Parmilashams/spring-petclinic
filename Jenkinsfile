pipeline {
    agent any

    triggers {
        // Every 5 minutes, only on Mondays
        cron('H/5 * * * 1')
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build + Test') {
            steps {
                // Use Maven Wrapper on Windows
                bat 'mvnw.cmd -B clean test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('JaCoCo Report') {
            steps {
                // Generate JaCoCo coverage report
                bat 'mvnw.cmd -B jacoco:report'
            }
        }

        stage('Archive Artifact') {
            steps {
                // Archive jar/war produced in target
                archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true
            }
        }
    }

    post {
        always {
            // Publish JaCoCo HTML report (requires HTML Publisher Plugin)
            publishHTML(target: [
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html',
                reportName: 'JaCoCo Coverage Report',
                keepAll: true,
                alwaysLinkToLastBuild: true,
                allowMissing: false
            ])
        }
    }
}
