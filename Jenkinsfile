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

                    // Print the Newman output to the console (for reference)
                    echo "Newman Output:\n${newmanOutput}"

                    // Split the output by lines
                    def outputLines = newmanOutput.split("\n")

                    // Count passed, failed, and skipped tests based on the symbols used in the output
                    def passedTests = outputLines.count { it.contains('✓') }  // Passed tests contain a tick (✓)
                    def failedTests = outputLines.count { it.contains('✘') }  // Failed tests contain a cross (✘)
                    def skippedTests = outputLines.count { it.contains('⚠') }  // Skipped tests contain a warning (⚠)

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
