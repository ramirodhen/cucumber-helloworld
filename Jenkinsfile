pipeline {
    agent any
    
    stages {
        stage('Get Code')
        {
            steps{
                sh 'ls -la'
                sh 'echo %WORKSPACE%'
            }
        }
        stage('Get Code') {
            steps {
                // Obtener código del repo
                // git branch: "arreglado", url: 'https://github.com/ramirodhen/cucumber-helloworld.git'
				script {
					scmVars = checkout scm
					echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
				}
            }
        }
        
        stage('Build&Test')
        {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh 'mvn test -e   -Dselenide.browser=chrome   -Dwebdriver.chrome.driver=/usr/bin/chromedriver   -Dselenide.browserBinary=/usr/bin/chromium-browser   -	Dselenide.headless=true'
                }
            }
        }
        
        stage('Results')
        {
            steps {
                cucumber fileIncludePattern: 'cucumber.json', jsonReportDirectory: 'target/', reportTitle: 'Cucumber Reports'
            }
        }
    }
}		
