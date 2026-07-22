pipeline {

  agent any

  environment {

    AZ_ACCOUNT = 'afyabutstorage'   

    AZ_SHARE   = 'afyabutshare'

  }

  stages {

    stage('Checkout') { steps { checkout scm } }

    stage('Deploy to ACI (file share)') {

      steps {

        withCredentials([string(credentialsId: 'azure-storage-key', variable: 'AZ_KEY')]) {

          sh '''

            az storage file upload-batch \

              --account-name "angel" --account-key "nzuBdp3ym43VW5YLGitXeXLL1AU+slHexCvn+MOO+30uA+8FkpJ+KFznephX/6g/7UBfVMqQr/tc+AStNruXwQ==" \

              --destination "afyabutshare" --source . \

              --pattern "*.html" --no-progress

          '''

        }

      }

    }

  }

}
