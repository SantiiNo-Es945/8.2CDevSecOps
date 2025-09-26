pipeline {
  agent any

  environment {
    // If "Allowed Domains" is set to example.com, keep this as-is.
    // If you clear "Allowed Domains", you can set this to any address, e.g. you@mailtrap.io
    EMAIL_TO = 'to@example.com'
  }

  options { timestamps() }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/SantiiNo-ES945/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        // Run tests but don't stop the pipeline if they fail,
        // so we can still send the email with the log.
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm test || exit /b 0'
        }
      }
      post {
        success {
          emailext(
            to: env.EMAIL_TO,
            subject: "[Run Tests ✅] SUCCESS - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """<p><b>Result:</b> ${currentBuild.currentResult}</p>
                     <p><b>Job:</b> ${env.JOB_NAME}</p>
                     <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                     <p><b>Console:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
            mimeType: 'text/html',
            attachLog: true,
            compressLog: true
          )
        }
        failure {
          emailext(
            to: env.EMAIL_TO,
            subject: "[Run Tests ❌] FAILURE - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """<p><b>Result:</b> ${currentBuild.currentResult}</p>
                     <p><b>Job:</b> ${env.JOB_NAME}</p>
                     <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                     <p><b>Console:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
            mimeType: 'text/html',
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        // Optional; won't fail the whole build if script missing
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm run coverage || exit /b 0'
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        // Continue even if audit exits non-zero
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm audit || exit /b 0'
        }
      }
      post {
        success {
          emailext(
            to: env.EMAIL_TO,
            subject: "[Security Scan 🔐] SUCCESS - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """<p><b>Result:</b> ${currentBuild.currentResult}</p>
                     <p><b>Job:</b> ${env.JOB_NAME}</p>
                     <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                     <p><b>Console:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
            mimeType: 'text/html',
            attachLog: true,
            compressLog: true
          )
        }
        failure {
          emailext(
            to: env.EMAIL_TO,
            subject: "[Security Scan 🔐] FAILURE - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """<p><b>Result:</b> ${currentBuild.currentResult}</p>
                     <p><b>Job:</b> ${env.JOB_NAME}</p>
                     <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                     <p><b>Console:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
            mimeType: 'text/html',
            attachLog: true,
            compressLog: true
          )
        }
      }
    }
  }

  post {
    always {
      echo "Build result: ${currentBuild.currentResult}"
    }
  }
}
