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
        stage('Build Docker Image') {
            when {
                expression { fileExists('Dockerfile') }
            }
            steps {
                // образ ми будуємо всередині Minikube (eval $(minikube docker-env))
                sh 'docker build -t springboot-pr7:latest .'
            }
        }
        stage('Deploy to Minikube') {
            steps {
                // застосовуємо Kubernetes-манифести з папки k8s/
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
                timeout(time: 5, unit: 'MINUTES') {
                    sh 'kubectl rollout status deployment/springboot-pr7'
                }
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
            echo 'CI/CD конвеєр успішно завершено!'
        }
        failure {
            echo 'CI/CD конвеєр завершено з помилкою.'
        }
    }
}
