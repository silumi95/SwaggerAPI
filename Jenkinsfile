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
                    // Run the Postman collection using Newman with both CLI and JUnit reporting
                    def newmanOutput = bat(script: "newman run ${COLLECTION_PATH} --reporters cli,junit --reporter-junit-export newman/report.xml", returnStdout: true).trim()

                    // Print the full output to Jenkins console to inspect the format
                    echo "Newman Output:\n${newmanOutput}"

                    // Optional: You can add custom logic to handle the results further.
                    // If you'd like, you can count passed, failed, and skipped tests here again:
                    def passedTests = 0
                    def failedTests = 0
                    def skippedTests = 0

                    // Here we process the results if you'd like to output them in a table format
                    newmanOutput.split("\n").each { line ->
                        if (line.contains('✔')) { passedTests++ }  // Passed tests contain a checkmark (✔)
                        if (line.contains('✘')) { failedTests++ }  // Failed tests contain a cross (✘)
                        if (line.contains('⚠')) { skippedTests++ }  // Skipped tests contain a warning (⚠)
                    }

                    // Print the summary to Jenkins console
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
