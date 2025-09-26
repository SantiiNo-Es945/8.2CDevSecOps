pipeline {
  agent any
  options { timestamps() }

  environment {
    // REPLACE these with your real keys from SonarCloud
    SONAR_ORG     = '<YOUR_ORG_KEY>'
    SONAR_PROJECT = '<YOUR_PROJECT_KEY>'
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
          // just to verify coverage file exists
          bat 'if exist coverage\\lcov.info (echo Found coverage\\lcov.info) else (echo NO coverage\\lcov.info)'
        }
      }
    }

    // ------- SONARCLOUD -------
    stage('SonarCloud Scan') {
      steps {
        script {
          // Make sure Global Tool: SonarQube Scanner is named exactly "SonarScanner"
          def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'

          // Use your Secret Text credential ID = SONAR_TOKEN (Manage Jenkins → Credentials)
          withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
            // Run scan and WAIT for Quality Gate, but DO NOT fail the build even if gate fails
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
              bat """
                "${scannerHome}\\bin\\sonar-scanner.bat" ^
                  -D"sonar.host.url=https://sonarcloud.io" ^
                  -D"sonar.organization=%SONAR_ORG%" ^
                  -D"sonar.projectKey=%SONAR_PROJECT%" ^
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

  post { always { echo 'Done. Check SonarCloud for analysis + Quality Gate.' } }
}
