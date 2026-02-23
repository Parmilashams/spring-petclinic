pipeline {
    agent any

    triggers {
        // Every 5 minutes on Mondays only
        cron('H/5 * * * 1')
    }

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build + Test + Package') {
            steps {
                // Creates target/*.jar and runs tests
                bat 'mvnw.cmd -B clean package'
            }
            post {
                always {
                    // Shows test results in Jenkins
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('JaCoCo Report') {
            steps {
                // Generates HTML report in target/site/jacoco
                bat 'mvnw.cmd -B jacoco:report'
            }
        }

        stage('Archive Artifact') {
            steps {
                // Archive the generated jar(s)
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            // Publish JaCoCo HTML report (requires HTML Publisher plugin)
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html',
                reportName: 'JaCoCo Coverage Report'
            ])
        }
    }
}
