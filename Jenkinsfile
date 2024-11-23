pipeline {
    agent any
    
    environment {
        // Path to the Newman results file
        RESULT_PATH = 'path/to/newman-results.json'
    }

    stages {
        stage('Run Tests') {
            steps {
                // Assuming you have a stage that runs Newman tests
                sh 'newman run collection.json -r json --reporter-json-export newman-results.json'
            }
        }

        stage('Display Test Results') {
            steps {
                script {
                    // Read the JSON result file using readFile and Groovy's JsonSlurper
                    def jsonContent = readFile(RESULT_PATH)
                    echo "Raw JSON content:\n${jsonContent}"

                    def jsonSlurper = new groovy.json.JsonSlurper()
                    def resultJson = jsonSlurper.parseText(jsonContent)

                    // Output results as a table to the console
                    echo "\nTest Results:"
                    def tableHeader = "Test Name | Status | Response Time (ms) | Assertions"
                    echo tableHeader
                    echo "---------------------------------------------------------"

                    // Check if executions array is empty
                    if (resultJson.run.executions.isEmpty()) {
                        echo "No tests found in the Newman results."
                    } else {
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
        }

        stage('Publish Results') {
            steps {
                echo 'Publish stage logic here (e.g., publishing to JUnit, Slack, etc.)'
            }
        }

        stage('Post Actions') {
            steps {
                echo 'Post actions after test results are processed'
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
    }
}
