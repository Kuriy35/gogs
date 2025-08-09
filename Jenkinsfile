pipeline {
    agent any

    environment {
        BINARY_NAME = "gogs"
        PATH = "/usr/local/bin:/usr/local/go/bin:/usr/bin:/bin:${env.PATH}"
        CGO_ENABLED = '1'
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
    }
}

