pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY') // Securely fetch API key
    }
    tools {
        nodejs "NodeJS14" // Adjust to match your Jenkins Node.js installation
    }
    stages {
        stage('Clone GitHub Repository') {
            steps {
                echo 'Cloning SwaggerAPI repository from GitHub...'
                git url: 'https://github.com/silumi95/SwaggerAPI.git', branch: 'main'
            }
        }
        stage('Install Postman CLI') {
            steps {
                echo 'Installing Postman CLI...'
                sh 'powershell.exe -NoProfile -InputFormat None -ExecutionPolicy AllSigned -Command "[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString(\'https://dl-cli.pstmn.io/install/win64.ps1\'))"'
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo 'Logging into Postman CLI...'
                sh 'postman login --with-api-key $POSTMAN_API_KEY'
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman collection from GitHub repository...'
                sh 'postman collection run ./SwaggerPetstore.postman_collection.json'
            }
        }
    }
    post {
        success {
            echo 'Postman tests completed successfully!'
        }
        failure {
            echo 'Postman tests failed!'
        }
    }
}
