pipeline {
    agent any

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
                    // Run the Newman collection and generate the report
                    sh 'newman run SwaggerPetstore.postman_collection.json --reporters cli,junit --reporter-junit-export newman/report.xml > newman_output.txt'

                    // Read the Newman output from the file
                    def newmanOutput = readFile('newman_output.txt')

                    // Initialize the table with headers
                    def tableOutput = """
| API Endpoint               | Status   | Response Time | Pet Name         |
|----------------------------|----------|---------------|------------------|
"""

                    // Split the output into lines for parsing
                    def lines = newmanOutput.split('\n')

                    // Iterate over the lines to extract relevant details
                    lines.each { line ->
                        // Check for lines that contain API endpoint info
                        if (line.contains('POST') || line.contains('PUT') || line.contains('GET') || line.contains('DELETE')) {
                            // Extract status and other info based on pattern
                            def status = line.contains('200 OK') ? 'Success' : 'Failed'
                            def petName = line.contains('Fluffy Updated') ? 'Fluffy Updated' : 'Fluffy'
                            
                            // Extract the API endpoint (assuming it's the second word in the line)
                            def endpoint = line.split(' ')[1] // Get the HTTP method and endpoint
                            
                            // Extract the response time (assuming it's in the second column)
                            def responseTime = line.split(',')[2]?.trim() ?: 'N/A' // Default to 'N/A' if no response time found

                            // Append the data to the table output
                            tableOutput += "| ${endpoint} | ${status} | ${responseTime} | ${petName} |\n"
                        }
                    }

                    // Print the formatted table in Jenkins console
                    echo tableOutput
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
