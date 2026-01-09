pipeline {
    agent any
    
    stages {
        stage('Get Code 1')
        {
            steps{
                sh 'ls -la'
                sh 'echo $WORKSPACE'
            }
        }
        stage('Get Code 2') {
            steps {
                // Obtener código del repo
                // git branch: "arreglado", url: 'https://github.com/ramirodhen/cucumber-helloworld.git'
				script {
					scmVars = checkout scm
					echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
				}
            }
        }
        
        stage('Build&Test') {
  steps {
    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
      sh '''
        set -eux

        mvn test -e \
          -Dselenide.browser=chrome \
          -Dwebdriver.chrome.driver=/usr/bin/chromedriver \
          -Dselenide.browserBinary=/usr/bin/chromium-browser \
          -Dselenide.headless=true \
          -Dselenide.browserCapabilities='{"goog:chromeOptions":{"args":["--headless=new","--no-sandbox","--disable-dev-shm-usage","--disable-gpu","--user-data-dir=/tmp/chrome-jenkins-${BUILD_TAG}"]}}'
      '''
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
