pipeline {
    agent any

    triggers {
        // REQUIRED: every 5 minutes on Thursday
        cron('H/5 * * * 4')

        // TEMP (use ONLY to quickly get 3 "Started by timer" runs, then switch back):
        // cron('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // Helpful on Windows to verify Java is available
        stage('Java Check') {
            steps {
                bat 'where java'
                bat 'java -version'
                bat 'echo JAVA_HOME=%JAVA_HOME%'
            }
        }

        stage('Build + Tests + Package') {
            steps {
                // Use Maven Wrapper for Windows
                bat 'mvnw.cmd -v'
                bat 'mvnw.cmd clean test package'
            }
        }

        stage('JaCoCo Coverage Report') {
            steps {
                // Generate JaCoCo report
                bat 'mvnw.cmd jacoco:report'
            }
            post {
                always {
                    // Publish JUnit test results
                    junit 'target\\surefire-reports\\*.xml'

                    // Archive the built artifact (JAR)
                    archiveArtifacts artifacts: 'target\\*.jar', fingerprint: true

                    // Archive JaCoCo HTML report files so you can screenshot them from Jenkins
                    archiveArtifacts artifacts: 'target\\site\\jacoco\\**', fingerprint: false
                }
            }
        }
    }
}
