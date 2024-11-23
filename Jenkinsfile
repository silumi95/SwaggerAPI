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
| HTTP Method | API Endpoint                 | Status | Response Time |
|:-----------:|:----------------------------:|:------:|---------------:|
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
                            def methodEndpointMatch = (line =~ /(POST|PUT|GET|DELETE)\s+([^\s]+)/)
                            def method = methodEndpointMatch ? methodEndpointMatch[0][1] : 'Unknown'
                            def endpointMatch = (line =~ /(POST|PUT|GET|DELETE)\s+(https?:\/\/[^\s]+)/)
                            def endpoint = endpointMatch ? endpointMatch[0][2] : 'Unknown'

                            // Extract base path (up to the first three segments of the URL)
                            def shortenedEndpoint = getBasePath(endpoint)
                            def status = line.contains('200 OK') ? 'Pass' : 'Fail'

                            // Append the data to the table output
                            tableOutput += "| ${method}| ${shortenedEndpoint}           | ${status} | ${responseTime} |\n"
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

// Helper function to shorten the endpoint (base path)
def getBasePath(String endpoint) {
    // Split the URL by '/'
    def segments = endpoint.split('/')

    // If there are fewer than 3 segments, return the whole URL (or first few segments)
    if (segments.size() < 3) {
        return endpoint
    }

    // Otherwise, return the first three segments joined by '/'
    def basePath = segments[0..2].join('/')

    // If base path exceeds 20 characters, shorten it with ellipsis
    return basePath.length() > 20 ? basePath.substring(0, 20) + "..." : basePath
}
