stage('Display Test Results') {
    steps {
        script {
            // Read the JSON result file using readFile and Groovy's JsonSlurper
            def jsonContent = readFile(RESULT_PATH)
            echo "Raw JSON content:\n${jsonContent}"

            def jsonSlurper = new groovy.json.JsonSlurper()
            def resultJson = jsonSlurper.parseText(jsonContent)

            // Output results as a table to the console
            echo "\nTest Results:"
            def tableHeader = "Test Name | Status | Response Time (ms) | Assertions"
            echo tableHeader
            echo "---------------------------------------------------------"

            // Iterate over each item in the results and print a row in the table
            resultJson.run.executions.each { execution ->
                def testName = execution.test.name
                def status = execution.assertions.any { it.passed } ? "Passed" : "Failed"
                def responseTime = execution.response.time
                def assertions = execution.assertions.size()

                // Print each test result in a table-like format
                echo "${testName} | ${status} | ${responseTime} | ${assertions}"
            }
        }
    }
}
