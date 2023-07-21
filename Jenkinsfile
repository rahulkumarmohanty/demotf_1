pipeline {
    agent any 
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
                sh 'terraform apply -auto-approve myplan.tfplan'
            }
        }
    }
}
