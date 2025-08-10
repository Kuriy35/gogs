pipeline {
    agent any

    environment {
        BINARY_NAME = "gogs"
        PATH = "/usr/local/bin:/usr/local/go/bin:/usr/bin:/bin:${env.PATH}"
        CGO_ENABLED = '1'
        SSH_KEY = "/var/lib/jenkins/.ssh/id_rsa_ansibleVM"
        ANSIBLE_USER = "vagrant"
        ANSIBLE_HOST_ADDRESS = "192.168.56.100"
        REMOTE_BINARY_PATH = "/tmp/${BINARY_NAME}_new"
        ANSIBLE_DIR_PATH = "/home/vagrant/ansible"
        PLAYBOOK_PATH_FROM_DIR = "playbooks/deployment_playbook.yml"
    }

    stages {
        stage('Check Tools') {
           steps {
                echo '----- Check if available go tools -----'
                sh 'go version'
                sh 'golangci-lint version'
            }
        }
        
        stage('Lint') {
            steps {
                echo '----- Run golangci-lint -----'
                sh 'golangci-lint run'
            }
        }

        stage('Test') {
            steps {
                echo '----- Run unit tests -----'
                sh 'go test ./...'
            }
        }

        stage('Build') {
            steps {
                echo '----- Build binary -----'
                sh "go build -o ${BINARY_NAME}"
            }
        }

        stage('Copy binary to Ansible VM') {
            steps {
                echo '----- Copying binary -----'
                sh """
                    scp -i ${SSH_KEY} ${BINARY_NAME} ${ANSIBLE_USER}@${ANSIBLE_HOST_ADDRESS}:${REMOTE_BINARY_PATH}
                """
            }
            
        }

        stage('Deploy using Ansible') { 
            steps { 
                echo '----- Run Ansible playbook -----'
                sh """
               	    ssh -i ${SSH_KEY} ${ANSIBLE_USER}@${ANSIBLE_HOST_ADDRESS} \\
                    "cd ${ANSIBLE_DIR_PATH} | \\
                    ansible-playbook ${PLAYBOOK_PATH_FROM_DIR} --extra-vars 'new_binary_name=${BINARY_NAME}_new'"
                """
            }
        }
    }
}

