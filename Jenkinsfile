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
                    bat 'newman run SwaggerPetstore.postman_collection.json --reporters cli,junit --reporter-junit-export newman/report.xml > newman_output.txt'

                    // Read the Newman output from the file
                    def newmanOutput = readFile('newman_output.txt')

                    // Initialize the table with headers
                    def tableOutput = """
| API Endpoint                                    | Status      | Response Time |
|-------------------------------------------------|-------------|---------------|
"""

                    // Split the output into lines for parsing
                    def lines = newmanOutput.split('\n')

                    // Iterate over the lines to extract relevant details
                    lines.each { line ->
                        // Only process lines containing HTTP method, status code, and response time
                        if (line.contains('ms]')) {
                            // Extract response time (between 'ms]' and 'ms')
                            def responseTimeMatch = (line =~ /(\d+ms)/)
                            def responseTime = responseTimeMatch ? responseTimeMatch[0][1] : 'N/A'

                            // Try to capture the HTTP method and endpoint (assuming it's in the line starting with the method)
                            def endpointMatch = (line =~ /(POST|PUT|GET|DELETE)\s+([^\s]+)/)
                            def endpoint = endpointMatch ? endpointMatch[0][2] : 'Unknown'

                            // Extract pet name if available (customize based on your actual response content)
                            def petName = line.contains('Fluffy Updated') ? 'Fluffy Updated' : 'Fluffy'

                            // Status based on response time or other indicators (use success or failure based on response)
                            def status = line.contains('200 OK') ? 'Success' : 'Failed'

                            // Append the data to the table output
                            tableOutput += "| ${endpoint} | ${status} | ${responseTime} |\n"
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
