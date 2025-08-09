pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checkout code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
