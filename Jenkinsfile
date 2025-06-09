pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Збірка') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean compile'
            }
        }
        stage('Тестування') {
            steps { sh './mvnw test' }
            post { always { junit 'target/surefire-reports/*.xml' } }
        }
        stage('Упакування') {
            steps { sh './mvnw package -DskipTests' }
        }
        stage('Архівація артефактів') {
            steps { archiveArtifacts artifacts: 'target/*.jar', fingerprint: true }
        }
    }

    post {
        success { echo 'CI Pipeline успішно завершено!' }
        failure { echo 'CI Pipeline завершено з помилкою.' }
    }
}