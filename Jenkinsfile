pipeline {
    agent any 
    environment {
        INFRACOST_API_KEY = "ico-v188MHnFUuLsKsSNOYLXfIhnL7ANIkaw"
    }

    parameters{
        string(name: 'ARM_TENANT_ID', description: 'Enter the tenant id')
        string(name: 'ARM_CLIENT_ID', description: 'Enter the client id')
        string(name: 'ARM_CLIENT_SECRET', description: 'Enter the client secret')
        string(name: 'ARM_SUBSCRIPTION_ID',description: 'Enter the subscription id')
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
                sh 'az login --service-principal --username ${ARM_CLIENT_ID} --password ${ARM_CLIENT_SECRET} --tenant ${ARM_TENANT_ID}'
                sh 'az account set --subscription ${ARM_SUBSCRIPTION_ID}'
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
              cleanWs()
            }
          }
        }
    }
}
