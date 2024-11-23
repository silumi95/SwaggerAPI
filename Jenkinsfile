pipeline {
    agent any

    environment {
        // Define the path to your Postman collection in the GitHub repo
        COLLECTION_PATH = 'SwaggerPetstore.postman_collection.json'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the public GitHub repository from the main branch
                git branch: 'main', url: 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        stage('Install Node.js') {
            steps {
                script {
                    // Install Node.js only if it's not already installed on the agent
                    if (!isNodeInstalled()) {
                        bat 'npm install -g node'
                    }
                }
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
                    // Run the Postman collection using Newman with both CLI and JUnit reporting
                    def newmanOutput = bat(script: "newman run ${COLLECTION_PATH} --reporters cli,junit --reporter-junit-export newman/report.xml", returnStdout: true).trim()

                    // Debug: Print the full output of the Newman run
                    echo "Newman Output:\n${newmanOutput}"

                    // Debug: Check if JUnit XML report was created
                    if (fileExists('newman/report.xml')) {
                        echo "JUnit report file generated."
                    } else {
                        echo "No JUnit report file generated. Something went wrong with Newman."
                    }

                    // Initialize counters for test results
                    def passedTests = 0
                    def failedTests = 0
                    def skippedTests = 0

                    // Process the Newman output to count passed, failed, and skipped tests
                    newmanOutput.split("\n").each { line ->
                        // Clean the line by removing odd characters like ✔ (check mark), ✘ (cross mark), ⚠ (warning symbol)
                        def cleanedLine = line.replaceAll('[✔✘⚠]', '').trim()

                        // Check the cleaned line for pass/fail/skipped status based on text
                        if (line.contains('✔')) { passedTests++ }  // Passed tests contain a checkmark (✔)
                        if (line.contains('✘')) { failedTests++ }  // Failed tests contain a cross (✘)
                        if (line.contains('⚠')) { skippedTests++ }  // Skipped tests contain a warning (⚠)

                        // Optionally, print the cleaned test result
                        if (cleanedLine) {
                            echo "Test Result: ${cleanedLine}"
                        }
                    }

                    // Print the summary in a table format
                    echo """
                    Test Results Summary:
                    +------------------+---------+
                    | Test Status      | Count   |
                    +------------------+---------+
                    | Passed           | ${passedTests}   |
                    | Failed           | ${failedTests}   |
                    | Skipped          | ${skippedTests}  |
                    +------------------+---------+
                    """

                    // Check if there are any failed tests and fail the build if needed
                    if (failedTests > 0) {
                        error "There are failing tests."
                    }
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                // Publish the JUnit test results for Jenkins to process and show in the test results page
                junit '**/newman/report.xml'

                // Debugging: Ensure the JUnit report is being processed correctly
                echo "JUnit results published"
            }
        }
    }

    post {
        always {
            // Final steps, for cleanup or notifications
            echo 'Pipeline finished'
        }
    }
}

// Helper function to check if Node.js is installed (Windows equivalent)
def isNodeInstalled() {
    try {
        bat 'node -v'
        return true
    } catch (Exception e) {
        return false
    }
}
