pipeline {
    agent any

    stages {
        stage('Збірка') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean compile'
            }
        }
        stage('Тестування') {
            steps {
                sh './mvnw test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Упакування') {
            steps {
                sh './mvnw package -DskipTests'
            }
        }
        /*
        stage('Збірка Docker-образу') {
            when {
                expression { fileExists('Dockerfile') }
            }
            steps {
                sh "docker build -t springboot-pr7:$BUILD_NUMBER ."
            }
        }
        */
        stage('Архівація артефактів') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'CI-конвеєр успішно завершено!'
        }
        failure {
            echo 'CI-конвеєр завершено з помилкою.'
        }
    }
}
