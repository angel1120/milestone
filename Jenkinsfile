pipeline {
    agent any

    environment {
        AZ_ACCOUNT = 'afyabutstorage'
        AZ_SHARE   = 'afyabutshare'
        ACI_URL    = 'afyabutaci.centralindia.azurecontainer.io'
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
                    curl -f $ACI_URL

                    curl -s $ACI_URL | grep "Hello"

                    # Uncomment this line to simulate a failed test
                    # exit 1
                '''
            }
        }

        stage('Deploy to Production') {
            steps {
               sh '''
                scp -o StrictHostKeyChecking=no index.html ubuntu@10.0.3.108:/tmp/index.html
                ssh -o StrictHostKeyChecking=no ubuntu@10.0.3.108 "sudo mv /tmp/index.html /usr/share/nginx/html/index.html"

                scp -o StrictHostKeyChecking=no index.html ubuntu@10.0.4.195:/tmp/index.html
                ssh -o StrictHostKeyChecking=no ubuntu@10.0.4.195 "sudo mv /tmp/index.html /usr/share/nginx/html/index.html"
            	'''
            }
        }
    }
}
