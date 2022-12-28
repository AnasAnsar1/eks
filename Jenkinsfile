pipeline {
    agent { label "terraform" }
    stages {
        stage('vcs') {
            steps {
                git branch: 'dev', url: 'https://github.com/AnasAnsar1/eks.git'
            }
        }
        stage('terraform_infra') {
            steps {
                sh 'terraform init && terraform apply -auto-approve'
                sh 'aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)'
            }
        }
    }

}