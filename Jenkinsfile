properties([
    parameters([
        string(
            name: 'Environment',
            defaultValue: 'dev'
        ),
        choice(
            name: 'Terraform_Action',
            choices: ['plan', 'apply', 'destroy']
        )
    ])
])

pipeline {

    agent any

    stages {

        stage('Preparing') {
            steps {
                sh 'echo Preparing...'
            }
        }

        stage('Git Pulling') {
            steps {
                git(
                    branch: 'master',   // Change to 'main' if your repo uses main
                    url: 'https://github.com/anushagrawal123/EKS-Terraform-GitHub-Actions.git'
                )
            }
        }

        stage('Init') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                    sh 'terraform -chdir=eks init -reconfigure'
                }
            }
        }

        stage('Validate') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                    sh 'terraform -chdir=eks validate'
                }
            }
        }

        stage('Action') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {

                    script {

                        if (params.Terraform_Action == 'plan') {

                            sh "terraform -chdir=eks plan -var-file=${params.Environment}.tfvars"

                        } else if (params.Terraform_Action == 'apply') {

                            sh "terraform -chdir=eks apply -var-file=${params.Environment}.tfvars -auto-approve"

                        } else if (params.Terraform_Action == 'destroy') {

                            sh "terraform -chdir=eks destroy -var-file=${params.Environment}.tfvars -auto-approve"

                        } else {

                            error("Invalid Terraform Action")

                        }

                    }
                }
            }
        }
    }

    post {

        success {
            echo "Terraform ${params.Terraform_Action} completed successfully."
        }

        failure {
            echo "Terraform ${params.Terraform_Action} failed."
        }

        always {
            cleanWs()
        }
    }
}
