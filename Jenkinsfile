pipeline{
agent any

// parameters{
//     string(name: 'SPEC', defaultValue:"cypress/e2e/1-getting-started/todo.cy.js", description: "Enter the cypress script path that you want to execute")
//     choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
// }
tools {nodejs "Nodejs"}
parameters{
    // string(name: 'SPEC', defaultValue:"cypress/e2e/1-getting-started/todo.cy.js", description: "Enter the cypress script path that you want to execute")
    choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
    booleanParam(name: 'skip_test', defaultValue: false, description: 'Set to true to skip the test stage')
    booleanParam(name: 'skip_sonar', defaultValue: false, description: 'Set to true to skip the SonarQube stage')
    booleanParam(name: 'skip_jmeter', defaultValue: false, description: 'Set to true to skip the SonarQube stage')
}

stages {
        stage('Run automated tests') {
            when { expression { params.skip_test != true } }
            steps {
                echo "Running automated tests"
                sh 'npm prune'
                sh 'npm cache clean --force'
                sh 'npm i'
                sh 'npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator'
                sh 'npm run cypress:run --browser $BROWSER --headless'
            }
        }
        stage('SonarQube analysis') {
            steps {
                // echo 'SonarQube analysis'
                when { expression { params.skip_sonar != true } }
                steps {
                    script {
                            scannerHome = tool 'sonar-scanner';
                        }
                    withSonarQubeEnv('SonarCloud') { // If you have configured more than one global server connection, you can specify its name
                    sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage('JMeter Test') {
            steps {
                echo 'JMeter Test'
            }
        }
        stage('Perform manual testing') {
            steps {
                echo 'Perform manual testing'
            }
        }
    }
}
