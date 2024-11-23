pipeline {
    agent any
    
    stages {
        stage('Checkout Code') {
            steps {
                // Clone the GitHub repository containing your Postman collection
                git 'https://github.com/silumi95/SwaggerAPI.git'  // Replace with your GitHub repo URL
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    // Install Node.js and Newman if not installed
                    bat 'npm install -g newman'  // This is for running the command on Windows (use 'bat' for Windows)
                }
            }
        }
        
        stage('Run Postman Tests') {
            steps {
                script {
                    // Run the Postman collection using Newman
                    bat 'newman run SwaggerAPI/postman_collection.json'  // Path to your Postman collection file
                }
            }
        }
    }
    
    post {
        always {
            // Add actions to be performed after the pipeline (e.g., notifications, cleanup, etc.)
            echo 'Postman tests executed.'
        }
        
        success {
            echo 'Tests passed successfully!'
        }
        
        failure {
            echo 'Tests failed!'
        }
    }
}
