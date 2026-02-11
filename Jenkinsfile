pipeline {
    agent { label 'ec2' }

    environment {
        IMAGE_NAME = "aman9372/sample-app"
        IMAGE_TAG  = "v1.${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                  chmod +x app.sh
                  ./app.sh
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                  chmod +x test.sh
                  ./test.sh
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                  docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                  docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('docker container') {
            steps { 
                sh '''
                docker run -d -p 8080:80 --name sample-app aman9372/sample-app:$IMAGE_TAG
                '''
            }
        }    

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'index.html'
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully 🎉"
        }
        failure {
            echo "Pipeline failed ❌"
        }
        always {
            sh 'docker logout || true'
        }
    }
}
