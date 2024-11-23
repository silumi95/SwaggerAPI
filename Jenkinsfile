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
                    // Run the Postman collection using Newman with CLI and JUnit reporter
                    bat "newman run ${COLLECTION_PATH} --reporters cli,junit --reporter-junit-export newman/report.xml"
                    
                    // Print the result to the Jenkins console for debugging purposes (optional)
                    echo "Newman tests have finished running"
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                // Publish JUnit test results to Jenkins
                junit '**/newman/report.xml'  // Adjust path if necessary
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
