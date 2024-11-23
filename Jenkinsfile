pipeline {
    agent any
    
    tools {
        git 'Git'  // Refer to the Git tool you configured
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/silumi95/SwaggerAPI.git'
            }
        }

        stage('Run Postman Tests') {
            steps {
                script {
                    // Run Postman collection using Newman (if Node.js and Newman are installed)
                    bat 'newman run SwaggerAPI/postman_collection.json'
                }
            }
        }
    }
}
