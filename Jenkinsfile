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
        
        stage('Diag Chrome') {
  steps {
    sh '''
      set -eux
      whoami
      uname -a
      arch

      which chromium-browser || true
      which chromium || true
      which google-chrome || true
      which chromedriver || true

      /usr/bin/chromedriver --version || true
      chromium-browser --version || true
      chromium --version || true
      google-chrome --version || true

      file /usr/bin/chromedriver || true
      ldd /usr/bin/chromedriver | head -n 50 || true
    '''
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
