stage('Run Postman Collection') {
    steps {
        echo '==============================================='
        echo 'Step 5: Running Postman collection from GitHub...'
        echo '==============================================='
        script {
            def jsonReportPath = "${WORKSPACE}\\newman_report.json"
            def htmlReportPath = "${WORKSPACE}\\report.html"

            // Run Newman with JSON and HTML reporters
            powershell """
                newman run ${params.COLLECTION_PATH} \
                    --reporters cli,htmlextra,json \
                    --reporter-htmlextra-export "${htmlReportPath}" \
                    --reporter-json-export "${jsonReportPath}"
            """

            // Read and parse the JSON report
            if (fileExists(jsonReportPath)) {
                def testResults = readJSON file: jsonReportPath

                // Extract summary details
                def totalTests = testResults.run.stats.tests.total
                def failedTests = testResults.run.stats.tests.failed
                def passedTests = totalTests - failedTests
                echo "========== Test Results =========="
                echo "Total Tests: ${totalTests}"
                echo "Passed Tests: ${passedTests}"
                echo "Failed Tests: ${failedTests}"

                // Optionally, list failed tests
                if (failedTests > 0) {
                    echo "========== Failed Tests =========="
                    testResults.run.executions.each { execution ->
                        execution.assertions.each { assertion ->
                            if (assertion.error) {
                                echo "Test: ${execution.item.name} - ${assertion.assertion}"
                                echo "Error: ${assertion.error.message}"
                            }
                        }
                    }
                }
            } else {
                echo "Test results JSON file not found!"
            }
        }
        echo 'Postman collection run completed!'
        echo '==============================================='
    }
}
