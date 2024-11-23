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

                    // Print the full output to Jenkins console to inspect the format
                    echo "Newman Output:\n${newmanOutput}"

                    // Optional: Process the output to count passed, failed, and skipped tests
                    def passedTests = 0
                    def failedTests = 0
                    def skippedTests = 0

                    // Check if the output contains test statuses and increment the counters accordingly
                    newmanOutput.split("\n").each { line ->
                        if (line.contains('✔')) { passedTests++ }  // Passed tests contain a checkmark (✔)
                        if (line.contains('✘')) { failedTests++ }  // Failed tests contain a cross (✘)
                        if (line.contains('⚠')) { skippedTests++ }  // Skipped tests contain a warning (⚠)
                    }

                    // Ensure the output contains a summary (sometimes Newman output might not include a simple table)
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
