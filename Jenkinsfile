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
                sh 'terraform init'
                sh 'terraform apply myplan.tfplan --auto-approve'
            }
        }
    }
}
