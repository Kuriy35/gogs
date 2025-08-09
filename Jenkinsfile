pipeline {
    agent any

    environment {
        BINARY_NAME = "gogs"
    }

    stages {
        stage('Lint') {
            steps {
                echo 'Run golangci-lint...'
                sh 'golangci-lint run'
            }
        }

        stage('Test') {
            steps {
                echo 'Run unit tests...'
                sh 'go test ./...'
            }
        }

        stage('Build') {
            steps {
                echo 'Build binary...'
                sh "go build -o ${BINARY_NAME}"
            }
        }
    }
}

