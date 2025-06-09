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
        stage('Build Docker Image') {
                    steps {
                        script {
                            sh 'eval $(minikube -p minikube docker-env)'
                            def img = "springboot-pr7:latest"
                            sh "docker build -t ${img} ."
                            echo "Built Docker image ${img}"
                        }
                    }
                }

                stage('Deploy to Minikube') {
                    steps {
                        script {
                            sh 'kubectl config use-context minikube'

                            sh 'kubectl apply -f k8s/deployment.yaml'
                            sh 'kubectl apply -f k8s/service.yaml'

                            timeout(time:5, unit:'MINUTES') {
                                sh 'kubectl rollout status deployment/springboot-pr7'
                            }
                        }
                    }
                }

    }

    post {
        success { echo 'CI Pipeline успішно завершено!' }
        failure { echo 'CI Pipeline завершено з помилкою.' }
    }
}