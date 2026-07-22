pipeline {
    agent any

    environment {
        AZ_ACCOUNT = 'afyabutstorage'
        AZ_SHARE   = 'afyabutshare'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Staging') {
            steps {
                withCredentials([string(credentialsId: 'azure-storage-key', variable: 'AZ_KEY')]) {
                    sh '''
                        az storage file upload-batch \
                          --account-name "$AZ_ACCOUNT" \
                          --account-key "$AZ_KEY" \
                          --destination "$AZ_SHARE" \
                          --source . \
                          --pattern "*.html" \
                          --no-progress
                    '''
                }
            }
        }

        stage('Staging Test') {
            steps {
                sh '''
                    echo "Running staging tests..."
                    echo "Simulated test failure."
                    exit 1
                '''
            }
        }
    }
}
