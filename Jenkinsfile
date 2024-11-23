pipeline {
    agent any

    tools {
        nodejs 'NodeJS_Latest'  // Ensure NodeJS is installed, required for Newman
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the GitHub repository that contains the Postman collection
                git 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        stage('Install Newman') {
            steps {
                script {
                    // Install Newman globally
                    bat 'npm install -g newman'
                }
            }
        }

        stage('Run Postman Collection') {
            steps {
                script {
                    // Run the Postman collection from the cloned repo using Newman
                    bat 'newman run SwaggerAPI/postman_collection.json -r html --reporter-html-export newman-report.html'
                }
            }
        }

        stage('Archive Reports') {
            steps {
                // Archive the generated HTML report after the tests
                archiveArtifacts artifacts: 'newman-report.html', allowEmptyArchive: true
            }
        }
    }
}
