pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        FRONTEND_IMAGE = credentials('frontend-image')
        BACKEND_IMAGE = credentials('backend-image')
        GIT_BRANCH = 'dev'
        GIT_REPO = credentials('git-repo')
        REGION = credentials('region')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: GIT_BRANCH, url: GIT_REPO
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Frontend Docker Image') {
                    steps {
                        dir('ResumeBuilderAngular') {
                            script {
                                docker.build(FRONTEND_IMAGE)
                            }
                        }
                    }
                }
                stage('Build Backend Docker Image') {
                    steps {
                        dir('ResumeBuilderBackend') {
                            script {
                                docker.build(BACKEND_IMAGE)
                            }
                        }
                    }
                }
            }
        }

        stage('Push Docker Images') {
            parallel {
                stage('Push Frontend Docker Image') {
                    steps {
                        script {
                            docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                                docker.image(FRONTEND_IMAGE).push()
                            }
                        }
                    }
                }
                stage('Push Backend Docker Image') {
                    steps {
                        script {
                            docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                                docker.image(BACKEND_IMAGE).push()
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    withAWS(credentials: 'aws', region: REGION) {
                        sh 'kubectl set image deployment/frontend-deployment frontend=${FRONTEND_IMAGE}'
                        sh 'kubectl set image deployment/backend-deployment backend=${BACKEND_IMAGE}'
                    }
                }
            }
        }
    }
}
