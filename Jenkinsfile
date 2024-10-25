pipeline {
    agent any
    options {
            // This is required if you want to clean before build
            skipDefaultCheckout(true)
    }

    environment {
        REPOSITORY = "glory3333/spring-project_ci-cd"
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub')
        IMAGE_TAG = ""
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'develop', url: "https://github.com/gloryYam/backend.git"
            }
        }

        stage('Test') {
            steps {
                script {
                    sh "docker --version"
                    sh "docker compose --version"
                }
            }
        }

        stage('Set Image Tag') {
            steps {
                script {
                    // Set image tag based on branch name
                    if (BRANCH_NAME == 'develop') {
                        IMAGE_TAG = "1.0.${BUILD_NUMBER}"
                    } else {
                        IMAGE_TAG = "0.0.${BUILD_NUMBER}"
                    }
                    echo "Image tag set to: ${IMAGE_TAG}"
                }
            }
        }

        stage('Building our image') {
            steps {
                script {
                    sh "docker build --no-cache -t ${REPOSITORY}:${IMAGE_TAG} ." // docker build
                }
            }
        }

        stage('Login') {
            steps {
                sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin" // docker hub 로그인
            }
        }

        stage('Deploy our image') {
            steps {
                script {
                    sh "docker push ${REPOSITORY}:${IMAGE_TAG}" // docker push
                }
            }
        }

        stage('Cleaning up') {
            steps {
                sh "docker rmi ${REPOSITORY}:${IMAGE_TAG}" // docker image 제거
            }
        }
    }

    post {
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                              [pattern: '.propsfile', type: 'EXCLUDE']])
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}