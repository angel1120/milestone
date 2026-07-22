pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying application to staging...'
                echo 'Staging deployment complete.'
            }
        }

        stage('Staging Test') {
            steps {
                sh '''
                    echo "Running staging tests..."
                    echo "Test failed!"
                    exit 1
                '''
            }
        }
    }
}

Successful Jenkinsfile

