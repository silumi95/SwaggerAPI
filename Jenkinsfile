pipeline {
    agent any
    environment {
        POSTMAN_API_KEY = credentials('POSTMAN_API_KEY') // Use credentials for sensitive information
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning Repository...'
                git url: 'https://github.com/silumi95/SwaggerAPI.git', branch: 'main'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo 'Installing Dependencies...'
                powershell '''
                    npm install -g newman newman-reporter-htmlextra
                '''
            }
        }
        stage('Run Postman Collection') {
            steps {
                echo 'Running Postman Collection...'
                powershell '''
                    newman run SwaggerPetstore.postman_collection.json --reporters cli,htmlextra --reporter-htmlextra-export reports/newman-report.html
                '''
            }
        }
        stage('Archive Reports') {
            steps {
                echo 'Archiving Newman Report...'
                archiveArtifacts artifacts: 'reports/*.html', allowEmptyArchive: true
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
