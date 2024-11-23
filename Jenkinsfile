pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY')
        DISABLE_GPU = 'true'
    }
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/silumi95/SwaggerAPI.git', description: 'Git repository URL')
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to clone')
        string(name: 'COLLECTION_PATH', defaultValue: './SwaggerPetstore.postman_collection.json', description: 'Path to Postman collection')
    }
    tools {
        nodejs "NodeJS_Latest"
    }
    stages {
        stage('Clone GitHub Repository') {
            steps {
                echo "Cloning repository: ${params.REPO_URL}, branch: ${params.BRANCH_NAME}"
                git url: params.REPO_URL, branch: params.BRANCH_NAME
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
        stage('Install Newman Reporter') {
            steps {
                echo 'Installing Newman HTML Reporter...'
                powershell 'npm install newman-reporter-htmlextra'
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
                echo 'Running Postman collection...'
                powershell """
                    newman run ${params.COLLECTION_PATH} --reporters cli,htmlextra --reporter-htmlextra-export "${WORKSPACE}\\report.html"
                """
            }
        }
        stage('Archive HTML Report') {
            steps {
                script {
                    def reportPath = "${WORKSPACE}\\report.html"
                    if (fileExists(reportPath)) {
                        archiveArtifacts artifacts: reportPath
                    } else {
                        echo 'Report not found, skipping archive step.'
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
