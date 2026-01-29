pipeline {
    agent none

    options {
        skipDefaultCheckout()
        timestamps()
    }

    environment {
        APP_NAME   = 'bible-admin'
        IMAGE_NAME = 'melodion/bible-admin:latest'
    }

    stages {

        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Build Backend (Maven)') {
            agent {
                docker {
                    image 'maven:3.9.9-eclipse-temurin-21'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh '''
                  ./mvnw -Pprod clean verify -DskipTests
                '''
            }
        }

        stage('Build Docker Image') {
            agent any
            steps {
                sh '''
                  docker build -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Run Container') {
            agent any
            steps {
                sh '''
                  docker stop ${APP_NAME} || true
                  docker rm ${APP_NAME} || true
                  docker run -d \
                    --name ${APP_NAME} \
                    -p 8081:8080 \
                    ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        success {
            echo '✅ JHipster build & deploy sukses'
        }
        failure {
            echo '❌ JHipster build gagal'
        }
    }
}
