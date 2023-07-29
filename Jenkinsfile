pipeline {
    agent any 
    environment {
        INFRACOST_API_KEY = "ico-v188MHnFUuLsKsSNOYLXfIhnL7ANIkaw"
    }
    parameters {
        choice choices: ['2ed1a4b1-8d67-48fb-8ef6-0d8fa4ab6a5d'], description: 'Choose any of the subscription id..', name: 'ARM_SUBSCRIPTION_ID'
    }
    stages {
        stage('copying artifacts') {
            steps {
                script {
                    copyArtifacts projectName: 'test1'
                }
            }
        }
        stage('Terraform apply') {
            steps {
                sh 'terraform init --backend-config=backend-conffinal.tfvars --force-copy'
                sh 'terraform apply -auto-approve myplan.tfplan'
            }
        }
        stage('Send Notification to Slack') {
          steps {
            script {
              def channel = "D04TUD02BNY" // replace with your desired Slack channel
              def slackCredentialId = "SLacktoken" // replace with the ID of your Slack API token credential in Jenkins
              def buildStatus = currentBuild.result
              def slackMessage = """
                *Jenkins Build ${buildStatus}*
                Build Number: ${env.BUILD_NUMBER}
                Build URL: ${env.BUILD_URL}
              """
              slackSend(channel: channel, message: slackMessage, color: buildStatus == 'SUCCESS' ? 'good' : 'danger', tokenCredentialId: slackCredentialId)
            }
          }
        }
    }
}
