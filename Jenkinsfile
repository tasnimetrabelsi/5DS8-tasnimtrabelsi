pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        IMAGE = 'tasnimetrabelsi/trabelsitasnim-5ds8-gestionprojets'
    }

    stages {
        stage('Maven Clean & Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Maven Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE}:${BUILD_NUMBER} -t ${IMAGE}:latest ."
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push ${IMAGE}:${BUILD_NUMBER}"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }

        stage('Deploy (Docker Compose)') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }

    post {
        success { echo 'Pipeline terminé avec succès' }
        failure { echo 'Le pipeline a échoué' }
    }
}
