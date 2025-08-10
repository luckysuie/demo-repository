pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/luckysuie/demo-repository.git', branch: 'master'
            }
        }
        stage('Trivia Scanning') {
            steps {
                sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL . --output trivy-report.txt || true'
                archiveArtifacts artifacts: 'trivy-report.txt', allowEmptyArchive: true
            }
        }
        stage('Login to Azure') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'azure-sp', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD'),
                    string(credentialsId: 'azure-tenant', variable: 'AZURE_TENANT')
                ]) {
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $AZURE_TENANT
                    '''
                }
            }
        }
        stage('Initialize Terraform') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }
        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }
        stage('Terraform Apply') {
            steps {
                sh 'terraform apply -auto-approve tfplan'
            }
        }
        stage('Prometheus Monitoring') {
            steps {
                sh '''
                # Restart Prometheus service to apply changes without requiring password
                sudo systemctl restart prometheus
                '''
            }
        }
    }
}
