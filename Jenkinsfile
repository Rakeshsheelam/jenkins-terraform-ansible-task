pipeline {
    agent any

    stages {
        

        stage('Checkout') {
            steps {
                deleteDir()
                sh 'echo cloning repo'
                sh 'git clone https://github.com/Rakeshsheelam/jenkins-terraform-ansible-task.git' 
            }
        }
        
        stage('Terraform Apply') {
            steps {
                script {
                    dir('/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/') {
                    sh 'pwd'
                    sh 'terraform init'
                    sh 'terraform validate'
                    // sh 'terraform destroy -auto-approve'
                       sh 'terraform plan'
                    sh 'terraform apply -auto-approve'
                    }
                }
            }
        }
        
        stage('Ansible Deployment') {
            steps {
                script {
                   sleep '360'
                    ansiblePlaybook becomeUser: 'ec2-user', credentialsId: 'AWS', disableHostKeyChecking: true, installation: 'ansible', inventory: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/inventory.yaml', playbook: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/amazon-playbook.yml', vaultTmpPath: ''
                    ansiblePlaybook become: true, credentialsId: 'AWS', disableHostKeyChecking: true, installation: 'ansible', inventory: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/inventory.yaml', playbook: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/ubuntu-playbook.yml', vaultTmpPath: ''
                }
            }
        }
    }
}
