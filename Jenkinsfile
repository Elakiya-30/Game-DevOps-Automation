pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

    stages {

        stage('Code Pull from GitHub') {
            steps {
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/Elakiya-30/Game-DevOps-Automation.git'
            }
        }

        stage('Terraform Init') {
            steps {
                dir('terraform/back-setup') {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('terraform/back-setup') {
                    sh 'terraform plan'
                }
            }
        }

        stage('Approval Stage') {
            steps {
                input message: 'Do you want to apply Terraform?', ok: 'Yes'
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform/back-setup') {
                    sh 'terraform apply -auto-approve'
                }
            }
        }
        stage('Sleep') {
          steps {
              echo 'Waiting for infra...'
                sleep(time: 3, unit: 'MINUTES')
         }
      }

        stage('Run Ansible') {
            steps {
                dir('ansible') {
                    sh 'ansible-playbook playbook.yml'
                }
            }
        }

        stage('Deploy Game') {
            steps {
                echo 'Game deployed via Ansible'
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl -I http://localhost'
            }
        }
    }

    post {
        failure {
            echo 'Build Failed! Rolling back...'
            dir('terraform/back-setup') {
                sh 'terraform destroy -auto-approve'
            }
        }
    }
}
