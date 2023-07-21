pipeline {
    agent any 
    environment {
        INFRACOST_API_KEY = "ico-v188MHnFUuLsKsSNOYLXfIhnL7ANIkaw"
        ARM_CLIENT_ID = "853d1487-f659-4a81-8788-cfb52f62fe0f"
        ARM_CLIENT_SECRET = "jCt8Q~jiQXxENivxnHYZOAcPpwtmxtBML4nDFadC"
        ARM_TENANT_ID = "1e0b7619-b37d-4b53-a46b-35a1180b4632"
        ARM_SUBSCRIPTION_ID = "780f8b48-d256-4702-b9fd-0d0fd40a21a8"
    }
    stages {
        stage('copying artifacts') {
            steps {
                script {
                    copyArtifacts projectName: 'testing_pipe_1'
                }
            }
        }
        stage('Terraform apply') {
            steps {
                sh 'az login --service-principal --username ${ARM_CLIENT_ID} --password ${ARM_CLIENT_SECRET} --tenant ${ARM_TENANT_ID}'
                sh 'az account set --subscription ${ARM_SUBSCRIPTION_ID}'
                sh 'terraform init'
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
