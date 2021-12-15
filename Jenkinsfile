pipeline {
    agent any
    environment {
        REGISTRY = "amalkuriakose/fastapi-demo-app"
        REGISTRY_CREDENTIAL = "dockerhubcred"
        dockerImage = ''
        APP_VERSION = 'v0.0.0'
    }
    stages {
        stage('Clean') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh "docker kill \$(docker ps -q)"
                        sh "docker rm \$(docker ps -a -q)"
                        sh "docker rmi -f \$(docker images -q)"
                    }
                }
            }
        }
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'githubcred', url: 'https://github.com/amalkuriakose/fastapi-demo-app']]])
            }
        }
        stage('Versioning') {
            steps {
                script {
                    APP_VERSION = readFile("version.txt")
                    echo "Version - ${APP_VERSION}"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("${REGISTRY}")
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry( '', REGISTRY_CREDENTIAL) {
                        dockerImage.push("${APP_VERSION}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
        stage('Test') {
            steps {
                sh "docker run -d -p 8000:8000 ${REGISTRY}:${APP_VERSION}"
            }
        }
    }
    post {
        always {
            sh 'ls -la'
            cleanWs()
            sh 'ls -la'
        }
    }
}