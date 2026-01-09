pipeline {
    agent { label 'ec2' }
    
    environment  {
        IMAGE_NAME = "aman9372/sample-app-html" 
        IMAGE_TAG = "v1.${BUILD_NUMBER}" 
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
        
        stage('Docker build') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }
        
        stage('Docker login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub-credential',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }    
            }
        }
        
        stage('docker push') {
            steps {
                sh '''
                docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

    post {
        success {
            echo "Pipeline completed successfully 🎉"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}

