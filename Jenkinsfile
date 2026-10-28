
pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'hanusharma8938'
        IMAGE_NAME = 'hanush8938'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    test -f app.py
                    test -f Dockerfile
                    test -f requirements.txt
                    python3 -m py_compile app.py
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                      -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER} \
                      -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest .
                '''
            }
        }

     
stage('Login to Docker Hub') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'DOCKERHUB',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )
        ]) {
            sh '''
                set +x
                echo "$DOCKER_PASS" | docker login \
                    -u "$DOCKER_USER" --password-stdin
            '''
        }
    }
}


        stage('Push to Docker Hub') {
            steps {
                sh '''
                    docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER}
                    docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Build and Docker Hub push successful!'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins console log.'
        }
        always {
            sh 'docker logout || true'
        }
    }
}
