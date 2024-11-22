pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY') // Securely stored Postman API key
        DISABLE_GPU = 'true' // Optional environment variable, add usage if needed
    }
    tools {
        nodejs "NodeJS_Latest" // Node.js configured in Jenkins
    }
    stages {
        stage('Workspace Cleanup') {
            steps {
                echo 'Cleaning up the workspace...'
                deleteDir() // Clean up the workspace before starting
            }
        }
        stage('Clone GitHub Repository') {
            steps {
                echo 'Cloning SwaggerAPI repository from GitHub...'
                git url: 'https://github.com/silumi95/SwaggerAPI.git', branch: 'main'
            }
        }
        stage('Install Postman CLI') {
            steps {
                echo 'Installing Postman CLI on Windows...'
                powershell '''
                    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
                    iex ((New-Object System.Net.WebClient).DownloadString('https://dl-cli.pstmn.io/install/win64.ps1'))
                '''
            }
        }
        stage('Verify Tools') {
            steps {
                echo 'Verifying NodeJS and Postman CLI installation...'
                powershell 'node --version'
                powershell 'postman --version'
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo 'Logging into Postman CLI with API key...'
                powershell 'postman login --with-api-key "$env:POSTMAN_API_KEY"'
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman collection with detailed reporting...'
                powershell '''
                    postman collection run .\\SwaggerPetstore.postman_collection.json `
                    --reporters cli,junit `
                    --reporter-junit-export .\\results.xml
                '''
            }
        }
        stage('Publish Test Results') {
            steps {
                echo 'Publishing JUnit test results...'
                junit 'results.xml'  // Points to the generated results.xml file
            }
        }
    }
    post {
        always {
            echo 'Archiving all files for debugging...'
            archiveArtifacts artifacts: '**/*', allowEmptyArchive: true // Archive all workspace files for inspection
        }
        success {
            echo 'Postman tests completed successfully!'
        }
        failure {
            echo 'Postman tests failed!'
        }
    }
}
