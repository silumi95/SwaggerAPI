pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY')
        DISABLE_GPU = 'true' // Disable GPU if necessary
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
                powershell '''
                    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
                    iex ((New-Object System.Net.WebClient).DownloadString('https://dl-cli.pstmn.io/install/win64.ps1'))
                '''
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo 'Logging into Postman CLI...'
                powershell 'postman login --with-api-key $POSTMAN_API_KEY'
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman collection from GitHub repository...'
                powershell 'newman run ./SwaggerPetstore.postman_collection.json --reporters cli,html --reporter-html-export ./report.html'
            }
        }
        stage('Archive HTML Report') {
            steps {
                echo 'Archiving Postman HTML Report...'
                archiveArtifacts artifacts: 'report.html', allowEmptyArchive: true
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
