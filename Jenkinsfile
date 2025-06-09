pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Збірка') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Тестування') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Упакування') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
        stage('Архівація артефактів') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline успішно завершено!'
        }
        failure {
            echo 'CI Pipeline завершено з помилкою.'
        }
    }
}
