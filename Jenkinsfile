pipeline {
    agent { label 'ec2' }

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

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'build/index.html'
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
    }
}

