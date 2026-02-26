pipeline {
    agent any

    tools {
        // MUST match the name in Manage Jenkins -> Tools
        jdk 'JDK25'
    }

    triggers {
        // REQUIRED: every 5 minutes on Thursday
        cron('H/5 * * * 4')

        // TEMP (use only if today is not Thursday to capture 3 timer builds, then switch back)
        // cron('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Java Check (JDK from Jenkins Tools)') {
            steps {
                bat 'echo JAVA_HOME=%JAVA_HOME%'
                bat 'where java'
                bat 'java -version'
            }
        }

        stage('Build + Tests + Package') {
            steps {
                // Force wrapper to re-download if needed
                bat 'if exist ".mvn\\wrapper\\maven-wrapper.jar" del /f /q ".mvn\\wrapper\\maven-wrapper.jar"'
                bat 'mvnw.cmd -v'
                bat 'mvnw.cmd clean test package'
            }
        }

        stage('JaCoCo Coverage Report') {
            steps {
                bat 'mvnw.cmd jacoco:report'
            }
            post {
                always {
                    junit 'target\\surefire-reports\\*.xml'
                    archiveArtifacts artifacts: 'target\\*.jar', fingerprint: true
                    archiveArtifacts artifacts: 'target\\site\\jacoco\\**', fingerprint: false
                }
            }
        }
    }
}
