/* groovylint-disable CompileStatic */
pipeline {
    agent any
    parameters {
        choice choices: ['chrome', 'firefox'], name: 'BROWSER', description: 'Select the browser to run the tests'
    }

    stages {
        stage('Start Grid') {
            steps {
                bat 'docker compose -f grid.yaml up --scale ' + params.BROWSER + '=1 -d'
            }
        }
        stage('Run Test') {
            steps {
                bat 'docker compose -f test-suite.yaml up --pull=always'
                script {
                    if (fileExists('output/flight-reservation/testng-failed.xml') || fileExists('output/vendor-portal/testng-failed.xml')) {
                        error('Test failed')
                    } else {
                        echo 'All tests passed'
                    }
                }
            }
        }
    }
    post {
        always {
            bat 'docker compose -f grid.yaml down'
            bat 'docker compose -f test-suite.yaml down'
            archiveArtifacts artifacts: 'output/flight-reservation/emailable-report.html', followSymlinks:false
            archiveArtifacts artifacts: 'output/vendor-portal/emailable-report.html', followSymlinks: false
        }
    }
}
