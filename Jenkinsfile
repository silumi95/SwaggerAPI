pipeline {
    agent any

    environment {
        // Define the path to your Postman collection in the GitHub repo
        COLLECTION_PATH = 'SwaggerPetstore.postman_collection.json'
        RESULT_PATH = 'newman-results.json'  // Path for saving JSON results
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the public GitHub repository from the main branch
                git branch: 'main', url: 'https://github.com/silumi95/SwaggerAPI.git'
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
                    // Run the Postman collection using Newman with the JSON reporter
                    bat "newman run ${COLLECTION_PATH} --reporters cli,json --reporter-json-export ${RESULT_PATH}"
                }
            }
        }

        stage('Publish Results') {
            steps {
                // Archive the JSON results as artifacts in Jenkins (optional for further use)
                archiveArtifacts artifacts: 'newman-results.json', allowEmptyArchive: true
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
