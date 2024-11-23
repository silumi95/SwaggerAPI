pipeline {
    agent any
    
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the repository from GitHub
                git 'https://github.com/silumi95/SwaggerAPI.git'  // Update with your repo URL
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    // Install Node.js and Newman if not installed (you may skip this if Node.js and Newman are already installed on the Jenkins server)
                    sh 'npm install -g newman'  // Install Newman
                }
            }
        }
        
        stage('Run Postman Tests') {
            steps {
                script {
                    // Run the Postman collection using Newman
                    sh 'newman run SwaggerAPI/postman_collection.json'  // Path to your Postman collection file
                }
            }
        }
    }
    
    post {
        always {
            // You can add actions like cleaning up or notifications here if needed
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
