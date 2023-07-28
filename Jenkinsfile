pipeline {
    agent any 
    environment {
        INFRACOST_API_KEY = "ico-v188MHnFUuLsKsSNOYLXfIhnL7ANIkaw"
        ARM_CLIENT_ID = "06e302f7-1f8b-4c2c-921a-daa2c3c2b630"
        ARM_TENANT_ID = "1e0b7619-b37d-4b53-a46b-35a1180b4632"
    }
    parameters {
        choice choices: ['2ed1a4b1-8d67-48fb-8ef6-0d8fa4ab6a5d'], description: 'Choose any of the subscription id..', name: 'ARM_SUBSCRIPTION_ID'
        string(name: 'ARM_CLIENT_SECRET', description: 'Enter the client secret')
    }
    stages {
        stage('copying artifacts') {
            steps {
                script {
                    copyArtifacts projectName: 'test'
                }
            }
        }
        stage('Terraform apply') {
            steps {
                sh 'az login --service-principal --username ${ARM_CLIENT_ID} --password ${ARM_CLIENT_SECRET} --tenant ${ARM_TENANT_ID}'
                sh 'az account set --subscription ${ARM_SUBSCRIPTION_ID}'
                sh 'terraform init --backend-config=backend-conffinal.tfvars --reconfigure'
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
