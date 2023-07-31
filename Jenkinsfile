pipeline {
    agent any 
    parameters{
        choice choices: ['Terraform apply'], description: 'Choose any one of the Terraform actions to perform..', name: 'terraformaction'
        choice choices: ['VM','Loadbalancer','CDN','ManagedDisk','Application gateway','Blob storage','nsg','postgres','postgres_cosmosdb','snapshot','vpn_vnet_gw','image','port addition'], description: 'Choose any one of the Resource to deploy in the Azure Environment..', name: 'resource'
        string(name: 'ARM_TENANT_ID', defaultValue: '73306657-9352-491f-8fcc-40bf911a22e9', description: 'Enter the tenant id')
        string(name: 'ARM_CLIENT_ID', defaultValue: '8599eb49-abbc-416f-aeed-1c33b31fe714', description: 'Enter the client id')
        string(name: 'ARM_CLIENT_SECRET', defaultValue: 'yrr8Q~B0yFyo3ZG58dDK87Jgx1VEEZUadi_1bcdG', description: 'Enter the client secret')
        string(name: 'ARM_SUBSCRIPTION_ID', defaultValue: 'd4d1547b-1e0c-4d70-9407-79b556cb3687', description: 'Enter the subscription id')
        string(name: 'tfvars',description: 'Enter the tfvars file name')
        string(name: 'account',description: 'Enter the account name')
        string(name: 'nsgid',description: 'Enter the nsg id')
    }
    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    switch(params.resource) {
                        case 'VM':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_vm']])
                        break
                        case 'CDN':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_cdn']]) 
                        break
                        case 'Blob storage':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_blob_storage']])
                        break
                        case 'Application gateway':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_app_gateway']])
                        break
                        case 'Loadbalancer':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_loadbalancer']])
                        break
                        case 'ManagedDisk':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_managed_disk']])
                        break
                        case 'nsg':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_nsg']])
                        break
                        case 'postgres':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_postgres']])
                        break
                        case 'postgres_cosmosdb':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_postgres_cosmosdb']])
                        break
                        case 'snapshot':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_snapshot']])
                        break
                        case 'vpn_vnet_gw':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_vpn_vnet_gw']])
                        break
                        case 'image':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_image']])
                        break
                        case 'port addition':
                            cleanWs()
                            checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'SanthaID', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/az_port_addition']])
                        break
                    }
                }
            }
        }

        stage('azure cli logging') {
            steps{
                sh 'az login --service-principal --username ${ARM_CLIENT_ID} --password ${ARM_CLIENT_SECRET} --tenant ${ARM_TENANT_ID}'
                sh 'az account set --subscription ${ARM_SUBSCRIPTION_ID}'
            }
        }

        stage('terraform state creation') {
            steps{
                script{
                    def content = "key = \"${account}/${resource}/tfstate${env.BUILD_NUMBER}\""
                    writeFile file: 'backend-conffinal.tfvars', text: content
                }
            }
        }
        
        stage('Terraform apply') {
            steps {
                sh 'az login --service-principal --username ${ARM_CLIENT_ID} --password ${ARM_CLIENT_SECRET} --tenant ${ARM_TENANT_ID}'
                sh 'az account set --subscription ${ARM_SUBSCRIPTION_ID}'
                sh 'terraform init --backend-config=backend-conffinal.tfvars --force-copy -reconfigure'
                sh 'terraform apply --auto-approve'
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
