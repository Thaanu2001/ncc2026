pipeline {
    agent any

    environment {
        GIT_REPO_URL = 'https://github.com/Thaanu2001/ncc2026.git'
        GIT_BRANCH = 'main'
        DOCKERHUB_REPO = 'thaanu2001/gscomp342'
        DOCKERHUB_CREDENTIALS = 'dockerhub'
        SONARQUBE_SERVER = 'sonarqube-server'
        DOCKERHUB_USERNAME = 'admin'
        DOCKERHUB_PASSWORD = 'Thaxnu@12345'
    }

    stages {
        stage('1. Pull Git Repository') {
            steps {
                git branch: "${env.GIT_BRANCH}", url: "${env.GIT_REPO_URL}"
            }
        }

        stage('2. Build Docker Images') {
            steps {
                script {
                    bat "docker build -t %DOCKERHUB_REPO%:latest -f app\\Dockerfile app"
                }
            }
        }

        stage('3. Configure SonarQube and Test') {
            steps {
                script {
                    withSonarQubeEnv("${env.SONARQUBE_SERVER}") {
                        bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
                    }
                }
            }
        }

        stage('4. Push Docker Image to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${env.DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        bat 'docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_PASSWORD%'
                        bat 'docker push %DOCKERHUB_REPO%:latest'
                    }
                }
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}