pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY')
        DISABLE_GPU = 'true' // Disable GPU hardware acceleration
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
                    try {
                        # Define the custom installation path within the Jenkins workspace
                        $installPath = "$env:WORKSPACE\\PostmanCLI"

                        # Ensure the installation folder exists
                        if (-not (Test-Path $installPath)) {
                            New-Item -ItemType Directory -Force -Path $installPath
                        }

                        # Download and install Postman CLI into the custom directory
                        iex ((New-Object System.Net.WebClient).DownloadString('https://dl-cli.pstmn.io/install/win64.ps1'))

                        # List the files in the installation folder to confirm success
                        Write-Host "Listing files in $installPath"
                        Get-ChildItem $installPath | Select-Object Name

                        # Check if Postman CLI executable exists after installation
                        if (Test-Path "$installPath\\postman-cli.exe") {
                            Write-Host "Postman CLI installed successfully."
                        } else {
                            Write-Host "Postman CLI not found after installation."
                            exit 1
                        }
                    }
                    catch {
                        Write-Host "An error occurred during installation: $_"
                        exit 1
                    }
                '''
            }
        }
        stage('Postman CLI Login') {
            steps {
                echo 'Logging into Postman CLI...'
                powershell '''
                    # Ensure Postman CLI is available in the custom directory
                    if (Test-Path "$env:WORKSPACE\\PostmanCLI\\postman-cli.exe") {
                        postman login --with-api-key $POSTMAN_API_KEY
                    } else {
                        Write-Host "Postman CLI not found. Exiting..."
                        exit 1
                    }
                '''
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman collection from GitHub repository...'
                powershell '''
                    # Ensure Postman CLI is available in the custom directory
                    if (-not (Test-Path "$env:WORKSPACE\\PostmanCLI\\postman-cli.exe")) {
                        Write-Host "Postman CLI not found. Exiting..."
                        exit 1
                    }

                    # Run the Postman collection and capture output
                    $output = postman collection run ./SwaggerPetstore.postman_collection.json --reporters=cli,json --reporter-json-export=result.json
                    
                    # Capture and print the output of the collection run
                    Write-Host "Postman collection run output: $output"
                    
                    # Check if result.json was created
                    if (-not (Test-Path 'result.json')) {
                        Write-Host "Postman collection run failed to generate result.json."
                        exit 1
                    }
                '''
            }
        }
    }
    post {
        success {
            echo 'Postman tests completed successfully!'
        }
        failure {
            echo 'Postman tests failed!'
            archiveArtifacts artifacts: 'result.json', allowEmptyArchive: true
        }
    }
}
