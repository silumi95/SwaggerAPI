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
                // Install Node.js if not already installed (optional)
                bat 'npm install -g node'
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
                    // Run the Postman collection using Newman with CLI output
                    def newmanOutput = bat(script: "newman run ${COLLECTION_PATH} --reporters cli", returnStdout: true).trim()

                    // Print the full output to Jenkins console to inspect the format
                    echo "Newman Output:\n${newmanOutput}"

                    // Split the output by lines
                    def outputLines = newmanOutput.split("\n")

                    // Debug: Print the first few lines to ensure we're getting the expected output
                    echo "First few lines of output:\n${outputLines.take(5).join("\n")}"

                    // Initialize counters
                    def passedTests = 0
                    def failedTests = 0
                    def skippedTests = 0

                    // Check for specific keywords in the output for passing, failing, and skipped tests
                    outputLines.each { line ->
                        if (line.contains('✔')) { passedTests++ }  // Passed tests contain a checkmark (✔)
                        if (line.contains('✘')) { failedTests++ }  // Failed tests contain a cross (✘)
                        if (line.contains('⚠')) { skippedTests++ }  // Skipped tests contain a warning (⚠)
                    }

                    // Print the table to console
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
                }
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
