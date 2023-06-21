pipeline{
agent any

// parameters{
//     string(name: 'SPEC', defaultValue:"cypress/e2e/1-getting-started/todo.cy.js", description: "Enter the cypress script path that you want to execute")
//     choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
// }
tools {nodejs "Nodejs"}
// tools {sonarScanner 'SonarQube'}
parameters{
    // string(name: 'SPEC', defaultValue:"cypress/e2e/1-getting-started/todo.cy.js", description: "Enter the cypress script path that you want to execute")
    choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
    booleanParam(name: 'skip_test', defaultValue: true, description: 'Set to true to skip the test stage')
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
                sh 'su apt-get install xvfb'  //Penso que so falta a instalação do xvfb que não esta a dar por falta de permissoes
                sh 'npm run cypress:run --browser $BROWSER --headless'
            }
        }
        stage('SonarQube analysis') {
            when { expression { params.skip_sonar != true } }
            steps {
                echo 'SonarQube analysis'
                script {
                    scannerHome = tool name: 'sonar-scanner';
                }
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=TestePratico -Dsonar.sources=. -Dsonar.login=d9b19b063804c7d6ed0043015658c75c0f7271b3 -Dsonar.exclusions='jmeter/*'" //Deveria estar num .env
                }
            }
        }
        stage('JMeter Test') {
            when { expression { params.skip_jmeter != true } }
            steps {
                echo 'JMeter Test'
                script {
                    //Jmeter copiado para dentro do docker
                    def jmeterHome = '/usr/share/jmeter'

                    def jmeterScript = './TestePraticojmx.jmx'

                    sh "${jmeterHome}/bin/jmeter -n -t ${jmeterScript} -l result.jtl"
                }
                 post {
                    always {
                        archiveArtifacts 'result.jtl'
                    }
                    success {
                        perfReport filterRegex:'', sourceDataFiles: 'result.jtl'
                    }
                }
            }
        }
        stage('Perform manual testing') {
            steps {
                echo 'Perform manual testing'
            }
        }
    }
}
