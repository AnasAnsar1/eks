pipeline {
    agent none
    stages {
        stage('k8s_infra') {
            agent { label 'terraform' }
            steps {
                git branch: 'dev', url: 'https://github.com/AnasAnsar1/eks.git'
                sh 'terraform init && terraform apply -auto-approve'
                sh 'aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)'
            }
        }
        stage('deployment') {
            agent { label 'terraform' }
            steps {
                git branch: 'dev', url: 'https://github.com/AnasAnsar1/saleor.git'
                dir("${env.WORKSPACE}/saleor/k8s-deployments/") {
                    sh 'kubectl apply -f dev-namesapce.yml'
                }
                dir("${env.WORKSPACE}/saleor/k8s-deployments/saleor/") {
                    sh 'kubectl apply -f saleor.yml'
                    sh 'kubectl apply -f svc-saleor.yml'
                }
                dir("${env.WORKSPACE}/saleor/k8s-deployments/db/") {
                    sh 'kubectl apply -f db.yml'
                    sh 'kubectl apply -f svc-db.yml'
                }
                dir("${env.WORKSPACE}/saleor/k8s-deployments/redis/") {
                    sh 'kubectl apply -f redis.yml'
                    sh 'kubectl apply -f svc-redis.yml'
                }
            }
        }
    }
}
