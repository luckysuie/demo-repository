pipeline 
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/luckysuie/demo-repository.git', branch: 'master'
            }
        } 
        stage('Inialize Terraform') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform validate') {
            steps {
                sh 'terraform validate'
            }
        }
    }
}