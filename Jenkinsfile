pipeline {
    agent any

   
    triggers {
        // Every 5 minutes on Thursday
        cron('H/5 * * * 4')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build + Tests + Package') {
            steps {
                // Petclinic uses Maven Wrapper
                bat './mvnw clean test package'
            }
        }

        stage('JaCoCo Coverage Report') {
            steps {
                // Generate JaCoCo report (usually created during test phase in this project)
                bat './mvnw jacoco:report'
            }
            post {
                always {
                    // Publish test results in Jenkins
                    junit 'target/surefire-reports/*.xml'

                    // Archive the built artifact
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
}
