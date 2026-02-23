pipeline {
    agent any
    
    tools {
        maven 'Maven-3.9'
    }
    
    environment {
        DOCKER_IMAGE = 'sucursal-app'
        CONTAINER_NAME = 'contenedor_sucursal'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/SeCordova/semana8-jenkins-pipeline.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p 8081:8080 ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }
}
