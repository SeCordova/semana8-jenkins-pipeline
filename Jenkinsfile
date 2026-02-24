pipeline {
    agent any

    tools {
        maven 'Maven-3.9'
    }

    environment {
        DOCKER_IMAGE = 'sucursal-app'
        CONTAINER_NAME = 'contenedor_sucursal'
        DOCKER_NETWORK = 'sucursal-network'

        DB_HOST = 'mysql-db'
        DB_NAME = 'sucursal_db'
        DB_USER = 'root'
        DB_PASS = 'admin123'   
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
                    docker network inspect ${DOCKER_NETWORK} >/dev/null 2>&1 || docker network create ${DOCKER_NETWORK}

                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true

                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      --network ${DOCKER_NETWORK} \
                      -p 8081:8080 \
                      -e SPRING_DATASOURCE_URL="jdbc:mysql://${DB_HOST}:3306/${DB_NAME}?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC" \
                      -e SPRING_DATASOURCE_USERNAME="${DB_USER}" \
                      -e SPRING_DATASOURCE_PASSWORD="${DB_PASS}" \
                      -e SPRING_JPA_DATABASE_PLATFORM="org.hibernate.dialect.MySQL8Dialect" \
                      ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }
}
