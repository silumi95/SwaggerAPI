pipeline {
    agent any

    environment {
        // Define the path to your Postman collection
        POSTMAN_COLLECTION_PATH = "SwaggerPetstore.postman_collection.json"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the repository...'
                script {
                    // Force checkout the main branch
                    sh 'git checkout main'
                }
            }}
        // Stage to clone the repository
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository from GitHub...'
                git 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        // Stage to run Postman tests via Newman
        stage('Run Postman Tests') {
            steps {
                echo 'Running Postman collection with Newman...'
                // Run the Postman collection using Newman. Adjust path as needed
                sh 'newman run ${POSTMAN_COLLECTION_PATH}'
            }
        }

        // Stage to generate a report (optional)
        stage('Generate Report') {
            steps {
                echo 'Generating Postman report...'
                // Run the collection and generate a report
                sh 'newman run ${POSTMAN_COLLECTION_PATH} --reporters cli,html --reporter-html-export newman-report.html'
                // Archive the generated report
                archiveArtifacts artifacts: 'newman-report.html', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo 'Postman tests completed successfully!'
        }
        failure {
            echo 'Postman tests failed!'
        }
        always {
            echo 'Pipeline finished.'
        }
    }
}
