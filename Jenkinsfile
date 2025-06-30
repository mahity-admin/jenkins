pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yourdockerhubusername/elcip-web"
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = 'dockerhub-creds' // Stored in Jenkins credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/mahity-admin/jenkins.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('Elcip-web') {
                    script {
                        sh """
                            docker build -t $DOCKER_IMAGE:$IMAGE_TAG .
                        """
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: env.DOCKER_CREDENTIALS_ID,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push $DOCKER_IMAGE:$IMAGE_TAG
                        """
                    }
                }
            }
        }

        // Optional
        stage('Notify ArgoCD') {
            steps {
                echo 'Here you can trigger ArgoCD sync, webhook, or CLI command.'
            }
        }
    }

    post {
        success {
            echo "Build and push completed successfully."
        }
        failure {
            echo "Build failed. Check logs."
        }
    }
}
