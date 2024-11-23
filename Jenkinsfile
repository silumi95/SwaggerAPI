pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        stage('Install Node.js') {
            steps {
                script {
                    // Install Node.js if not already installed
                    if (!isNodeInstalled()) {
                        bat 'npm install -g node'
                    }
                }
            }
        }

        stage('Install Newman') {
            steps {
                bat 'npm install -g newman'
            }
        }

        stage('Run Postman Collection') {
            steps {
                script {
                    // Run the Newman collection and generate the report
                    bat 'newman run SwaggerPetstore.postman_collection.json --reporters cli,junit --reporter-junit-export newman/report.xml > newman_output.txt'

                    // Read the output from Newman
                    def newmanOutput = readFile('newman_output.txt')

                    // Initialize the table with headers
                    def tableOutput = """
| HTTP Method | API Endpoint | Status | Response Time | Pet Name |
|-------------|--------------|--------|---------------|----------|
"""

                    // Split the output into lines for parsing
                    def lines = newmanOutput.split('\n')

                    // Iterate over the lines to extract relevant details
                    lines.each { line ->
                        if (line.contains('ms]')) {
                            // Extract response time (between 'ms]' and 'ms')
                            def responseTimeMatch = (line =~ /(\d+ms)/)
                            def responseTime = responseTimeMatch ? responseTimeMatch[0][1] : 'N/A'

                            // Match HTTP method and endpoint using improved regex
                            def endpointMatch = (line =~ /(POST|PUT|GET|DELETE)\s+(https?:\/\/[^\s]+)/)
                            def endpoint = endpointMatch ? endpointMatch[0][2] : 'Unknown'

                            // Shorten the endpoint if it exceeds 20 characters
                            def shortenedEndpoint = shortenEndpoint(endpoint)

                            // Handle Pet Name and Status
                            def petName = 'Fluffy' // Default pet name (customize based on your actual response)
                            def status = line.contains('200 OK') ? 'Success' : 'Failed'

                            // Append the data to the table output
                            tableOutput += "| ${endpoint.split(' ')[0]} | ${shortenedEndpoint} | ${status} | ${responseTime} | ${petName} |\n"
                        }
                    }

                    // Print the table in the Jenkins console
                    echo tableOutput
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                junit '**/newman/report.xml'
                echo "JUnit results published"
            }
        }
    }

    post {
        always {
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

// Helper function to shorten the endpoint
def shortenEndpoint(String endpoint) {
    def splitEndpoint = endpoint.split('/')
    def baseEndpoint = splitEndpoint[0..Math.min(2, splitEndpoint.size() - 1)].join('/')

    // If baseEndpoint exceeds 20 characters, shorten it with ellipsis
    return baseEndpoint.length() > 20 ? baseEndpoint.substring(0, 20) + "..." : baseEndpoint
}
