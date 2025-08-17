pipeline {
    agent any

    environment {
        IMAGE_NAME = "kuriy35/mygogsapp"
        IMAGE_TAG_LATEST = "latest"
        PATH = "/usr/local/bin:/usr/local/go/bin:/usr/bin:/bin:${env.PATH}"
        ANSIBLE_USER = "vagrant"
        ANSIBLE_HOST_ADDRESS = "192.168.56.100"
        ANSIBLE_DIR_PATH = "/home/vagrant/ansible"
        PLAYBOOK_PATH_FROM_DIR = "playbooks/deploy_with_docker_playbook.yml"
    }

    stages {
        stage('Check Tools') {
           steps {
                echo '----- Check if available docker -----'
                sh 'docker --version'
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

       // stage('Build Docker Image') {
        //    steps {
         //       echo '------- Build Docker Image -------'
          //      sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG_LATEST} ."
           // }
       // }

        stage('Push Docker Image') {
            steps {
                echo '----- Pushing Docker Image -----'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', 
                usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG_LATEST}
                        docker logout
                    """
                }
            }
            
        }

        stage('Deploy using Ansible') { 
            steps { 
                echo '----- Run Ansible playbook -----'
                withCredentials([sshUserPrivateKey(credentialsId: 'ansible-master-vm-key', 
                keyFileVariable: 'SSH_KEY')]) { 
                    sh """
                        ssh -i \$SSH_KEY ${ANSIBLE_USER}@${ANSIBLE_HOST_ADDRESS} "cd ${ANSIBLE_DIR_PATH} && ansible-playbook ${PLAYBOOK_PATH_FROM_DIR} --extra-vars 'IMAGE_NAME=${IMAGE_NAME} IMAGE_TAG=${IMAGE_TAG_LATEST}'"
                    """
                }
            }
        }
    }
}
