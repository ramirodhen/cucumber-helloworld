pipeline {
  agent any

  stages {
    stage('Get Code 1') {
      steps {
        sh 'ls -la'
        sh 'echo $WORKSPACE'
      }
    }

    stage('Get Code 2') {
      steps {
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

          echo "BUILD_TAG=$BUILD_TAG"
          echo "BUILD_NUMBER=$BUILD_NUMBER"
        '''
      }
    }

    stage('Build&Test') {
      steps {
        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
          sh '''
            set -eux

            # Elegir binario del navegador que exista de verdad en este nodo
            if command -v chromium-browser >/dev/null 2>&1; then
              BROWSER_BIN="$(command -v chromium-browser)"
            elif command -v chromium >/dev/null 2>&1; then
              BROWSER_BIN="$(command -v chromium)"
            elif command -v google-chrome >/dev/null 2>&1; then
              BROWSER_BIN="$(command -v google-chrome)"
            else
              echo "No encuentro chromium/chrome en este nodo"
              exit 1
            fi
            echo "Using browser binary: $BROWSER_BIN"

            # Perfil por build, sin caracteres raros (BUILD_TAG a veces rompe rutas)
            PROFILE_DIR="/tmp/chrome-jenkins-${BUILD_NUMBER}"
            rm -rf "$PROFILE_DIR"
            mkdir -p "$PROFILE_DIR"

            mvn test -e \
              -Dselenide.browser=chrome \
              -Dwebdriver.chrome.driver=/usr/bin/chromedriver \
              -Dselenide.browserBinary="$BROWSER_BIN" \
              -Dselenide.headless=true \
              -Dselenide.browserCapabilities='{"goog:chromeOptions":{"args":["--headless=new","--no-sandbox","--disable-dev-shm-usage","--disable-gpu","--user-data-dir='${PROFILE_DIR}'"]}}'
          '''
        }
      }
    }

    stage('Results') {
      steps {
        cucumber fileIncludePattern: 'cucumber.json', jsonReportDirectory: 'target/', reportTitle: 'Cucumber Reports'
      }
    }
  }
}
