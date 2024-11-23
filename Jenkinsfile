pipeline {
    agent any
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }
        stage('Run Postman Collection') {
            steps {
                script {
                    sh 'newman run your-collection.json -r cli,htmlextra --reporter-htmlextra-export reports/newman-report.html'
                }
            }
        }
        stage('Archive Reports') {
            steps {
                archiveArtifacts allowEmptyArchive: true, artifacts: 'reports/*.html'
            }
        }
    }
}
