pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY') // Securely fetch API key
    }
    tools {
        nodejs "NodeJS_Latest"
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
                script {
                    try {
                        // Ensure the PowerShell script downloads Postman CLI and doesn't rely on nohup
                        sh '''powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -Command "
                            [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
                            iex ((New-Object System.Net.WebClient).DownloadString('https://dl-cli.pstmn.io/install/win64.ps1'))
                        "'''
                    } catch (Exception e) {
                        error "Failed to install Postman CLI: ${e.getMessage()}"
                    }
                }
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo 'Logging into Postman CLI...'
                script {
                    try {
                        // Postman CLI login with the API Key
                        sh 'postman login --with-api-key $POSTMAN_API_KEY'
                    } catch (Exception e) {
                        error "Failed to login to Postman CLI: ${e.getMessage()}"
                    }
                }
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman collection from GitHub repository...'
                script {
                    try {
                        // Running the Postman collection
                        sh 'postman collection run ./SwaggerPetstore.postman_collection.json'
                    } catch (Exception e) {
                        error "Failed to run Postman collection: ${e.getMessage()}"
                    }
                }
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
