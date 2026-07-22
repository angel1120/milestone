pipeline {
    agent any

    environment {
        AZ_ACCOUNT  = 'afyabutstorage'
        AZ_SHARE    = 'afyabutshare'
        STAGING_URL = 'http://afyabutaci.centralindia.azurecontainer.io'
        SERVER1     = '10.0.4.195'
        SERVER2     = '10.0.3.108'
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
                    echo "Checking staging endpoint..."

                    curl -f "$STAGING_URL"

                    echo "Checking page content..."

                    curl -s "$STAGING_URL" | grep "Jenkins CI/CD Demo Success!"
                '''
            }
        }

        stage('Deploy to Production') {
            steps {
                sshagent(credentials: ['webservers-ssh-key']) {
                    sh '''
                        echo "Deploying to EC2 Server 1..."

                        scp -o StrictHostKeyChecking=no index.html ubuntu@$SERVER1:/tmp/index.html
                        ssh -o StrictHostKeyChecking=no ubuntu@$SERVER1 \
                        "sudo cp /tmp/index.html /var/www/html/index.html"

                        echo "Deploying to EC2 Server 2..."

                        scp -o StrictHostKeyChecking=no index.html ubuntu@$SERVER2:/tmp/index.html
                        ssh -o StrictHostKeyChecking=no ubuntu@$SERVER2 \
                        "sudo cp /tmp/index.html /var/www/html/index.html"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Production deployment was blocked.'
        }
    }
}
