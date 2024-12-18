pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS')  // ID from Jenkins credentials store
        AWS_SECRET_ACCESS_KEY = credentials('AWS')  // ID from Jenkins credentials store
    }

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
                        sh 'terraform destroy -auto-approve'
                        sh 'terraform apply -auto-approve'
                    }
                }
            }
        }

        stage('Ansible Deployment') {
            steps {
                script {
                    sleep '100'
                    // Use withCredentials block to inject SSH private key for Ansible
                    withCredentials([file(credentialsId: 'AWS_SSH_KEY', variable: 'SSH_PRIVATE_KEY')]) {
                        ansiblePlaybook(
                            becomeUser: 'ec2-user',  // or 'ubuntu' for Ubuntu instances
                            credentialsId: 'AWS',  // Your AWS credentials
                            disableHostKeyChecking: true,
                            installation: 'ansible',
                            inventory: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/inventory.yaml',
                            playbook: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/amazon-playbook.yml',
                            vaultTmpPath: '',
                            extraVars: [
                                ansible_ssh_private_key_file: "${SSH_PRIVATE_KEY}"  // Dynamically pass SSH key from Jenkins credentials
                            ]
                        )
                    }
                    withCredentials([file(credentialsId: 'AWS_SSH_KEY', variable: 'SSH_PRIVATE_KEY')]) {
                        ansiblePlaybook(
                            become: true,
                            credentialsId: 'AWS',
                            disableHostKeyChecking: true,
                            installation: 'ansible',
                            inventory: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/inventory.yaml',
                            playbook: '/var/lib/jenkins/workspace/challenge_ansible_jenkins/jenkins-terraform-ansible-task/ubuntu-playbook.yml',
                            vaultTmpPath: '',
                            extraVars: [
                                ansible_ssh_private_key_file: "${SSH_PRIVATE_KEY}"  // Dynamically pass SSH key from Jenkins credentials
                            ]
                        )
                    }
                }
            }
        }
    }
}
