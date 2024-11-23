pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY')
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning Repository...'
                git url: 'https://github.com/silumi95/SwaggerAPI.git', branch: 'main'
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman Collection...'
                powershell '''
                    npm install -g newman newman-reporter-htmlextra
                    newman run SwaggerPetstore.postman_collection.json --reporters cli,htmlextra
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}
