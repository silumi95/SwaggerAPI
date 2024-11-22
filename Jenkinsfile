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
                powershell '''
                    postman collection run ./SwaggerPetstore.postman_collection.json --reporters=cli,html,json --reporter-json-export=result.json
                '''
            }
        }
        stage('Parse Results') {
            steps {
                echo 'Parsing Postman test results...'
                script {
                    // Parse the results and convert to JUnit format
                    def result = readFile('result.json')
                    def jsonResult = readJSON text: result
                    def testCases = jsonResult.run.results
                    def failedTests = testCases.findAll { it.status == 'failed' }
                    def totalTests = testCases.size()

                    // Set build result based on the number of failed tests
                    if (failedTests.size() > 0) {
                        currentBuild.result = 'FAILURE'
                    } else {
                        currentBuild.result = 'SUCCESS'
                    }

                    // Optionally, print a summary to the console
                    echo "Total tests: ${totalTests}, Failed tests: ${failedTests.size()}"
                }
            }
        }
    }
    post {
        success {
            echo 'Postman tests completed successfully!'
            // Optional: You can archive the HTML report here if needed
        }
        failure {
            echo 'Postman tests failed!'
            // Optionally, you can attach the result.json file as an artifact for debugging
            archiveArtifacts artifacts: 'result.json', allowEmptyArchive: true
        }
    }
}
