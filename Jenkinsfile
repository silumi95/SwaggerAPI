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
                echo '==============================='
                echo 'Step 1: Cloning SwaggerAPI repository from GitHub...'
                echo '==============================='
                git url: 'https://github.com/silumi95/SwaggerAPI.git', branch: 'main'
                echo 'Repository cloned successfully!'
                echo '==============================='
            }
        }
        stage('Install Postman CLI') {
            steps {
                echo '==================================='
                echo 'Step 2: Installing Postman CLI...'
                echo '==================================='
                powershell '''
                    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
                    iex ((New-Object System.Net.WebClient).DownloadString('https://dl-cli.pstmn.io/install/win64.ps1'))
                '''
                echo 'Postman CLI installed successfully!'
                echo '==================================='
            }
        }
        stage('Install Newman Reporter') {
            steps {
                echo '========================================'
                echo 'Step 3: Installing Newman HTML Reporter...'
                echo '========================================'
                powershell '''
                    npm install newman-reporter-htmlextra
                '''
                echo 'Newman HTML Reporter installed successfully!'
                echo '========================================'
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo '=========================================='
                echo 'Step 4: Logging into Postman CLI...'
                echo '=========================================='
                powershell 'postman login --with-api-key $POSTMAN_API_KEY'
                echo 'Logged into Postman CLI successfully!'
                echo '=========================================='
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo '==============================================='
                echo 'Step 5: Running Postman collection from GitHub...'
                echo '==============================================='
                powershell '''
                    # Run Newman with the HTML reporter, specifying an absolute path for the report
                    newman run ./SwaggerPetstore.postman_collection.json --reporters cli,htmlextra --reporter-htmlextra-export "C:\\Jenkins\\workspace\\$JOB_NAME\\report.html"
                '''
                echo 'Postman collection run completed!'
                echo '==============================================='
            }
        }
        stage('Archive HTML Report') {
            steps {
                echo '======================================='
                echo 'Step 6: Archiving Postman HTML Report...'
                echo '======================================='
                script {
                    // Use absolute path for the report
                    def reportPath = 'C:\\Jenkins\\workspace\\$JOB_NAME\\report.html'
                    // Check if the report exists
                    def reportExists = fileExists reportPath
                    if (reportExists) {
                        echo 'Report found. Archiving...'
                        archiveArtifacts artifacts: reportPath, allowEmptyArchive: true
                        echo 'Report archived successfully!'
                    } else {
                        echo 'Report not found, skipping archive step.'
                    }
                }
                echo '======================================='
            }
        }
    }
    post {
        success {
            echo '============================================='
            echo 'Postman tests completed successfully!'
            echo '============================================='
        }
        failure {
            echo '============================================='
            echo 'Postman tests failed! Please check the logs.'
            echo '============================================='
        }
    }
}
