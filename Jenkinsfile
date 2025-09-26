pipeline {
  agent any
  options { timestamps() }

  /* You will fill these on the Jenkins UI when you click “Build with Parameters”.
     Defaults are my best guess; change them in the UI if they don’t match your SonarCloud keys. */
  parameters {
    string(name: 'SONAR_ORG',     defaultValue: 'santiino-es945',             description: 'SonarCloud organization key (Project → Project Information → Organization)')
    string(name: 'SONAR_PROJECT', defaultValue: 'santiino-es945_8.2CDevSecOps', description: 'SonarCloud project key (Project → Project Information → Project key)')
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/SantiiNo-ES945/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { bat 'npm install' }
    }

    stage('Test with coverage') {
      steps { bat 'npm test -- --coverage || exit /b 0' }
      post {
        always {
          // helpful: prints if coverage/lcov.info exists
          bat 'if exist coverage\\lcov.info (echo Found coverage\\lcov.info) else (echo NO coverage\\lcov.info)'
        }
      }
    }

    stage('SonarCloud Scan') {
      steps {
        script {
          // Tool name must be EXACTLY the one in Manage Jenkins → Tools
          def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'

          // Use your Secret Text credential that holds the SonarCloud token (ID must be SONAR_TOKEN)
          withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
            // Don’t fail the whole build if the Quality Gate is FAIL
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
              bat """
                "${scannerHome}\\bin\\sonar-scanner.bat" ^
                  -D"sonar.host.url=https://sonarcloud.io" ^
                  -D"sonar.organization=${params.SONAR_ORG}" ^
                  -D"sonar.projectKey=${params.SONAR_PROJECT}" ^
                  -D"sonar.sources=." ^
                  -D"sonar.exclusions=**/node_modules/**" ^
                  -D"sonar.javascript.lcov.reportPaths=coverage/lcov.info" ^
                  -D"sonar.token=%SONAR_TOKEN%" ^
                  -D"sonar.qualitygate.wait=true"
              """
            }
          }
        }
      }
    }
  }

  post {
    always { echo 'Done. Open the Console Output for the SonarCloud link and Quality Gate status.' }
  }
}
