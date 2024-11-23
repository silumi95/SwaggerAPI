pipeline {
    agent any

    environment {
        // Define the path to your Postman collection in the GitHub repo
        COLLECTION_PATH = 'SwaggerPetstore.postman_collection.json'
        RESULT_PATH = 'newman-results.json'  // Path for saving JSON results
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the public GitHub repository from the main branch
                git branch: 'main', url: 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        stage('Install Newman') {
            steps {
                // Install Newman using npm during the pipeline execution
                bat 'npm install -g newman'
            }
        }

        stage('Run Postman Collection') {
            steps {
                script {
                    try {
                        // Run the Postman collection using Newman with the JSON reporter
                        bat "newman run ${COLLECTION_PATH} --reporters cli,json --reporter-json-export ${RESULT_PATH}"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'  // Set the build status to failure if Newman run fails
                        throw e  // Rethrow the error to stop the pipeline
                    }
                }
            }
        }

        stage('Display Test Results') {
            steps {
                script {
                    // Parse the JSON result file
                    def resultJson = readJSON file: RESULT_PATH

                    // Output results as a table to the console
                    echo "\nTest Results:"

                    def tableHeader = "Test Name | Status | Response Time (ms) | Assertions"
                    echo tableHeader
                    echo "---------------------------------------------------------"

                    // Iterate over each item in the results and print a row in the table
                    resultJson.run.executions.each { execution ->
                        def testName = execution.test.name
                        def status = execution.assertions.any { it.passed } ? "Passed" : "Failed"
                        def responseTime = execution.response.time
                        def assertions = execution.assertions.size()
                        
                        // Print each test result in a table-like format
                        echo "${testName} | ${status} | ${responseTime} | ${assertions}"
                    }
                }
            }
        }

        stage('Publish Results') {
            steps {
                // Archive the JSON results as artifacts in Jenkins (optional for further use)
                archiveArtifacts artifacts: 'newman-results.json', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // Final steps, for cleanup or notifications
            echo 'Pipeline finished'
        }

        success {
            echo 'Tests ran successfully!'
        }

        failure {
            echo 'Tests failed. Please check the Newman results for more information.'
        }
    }
}
