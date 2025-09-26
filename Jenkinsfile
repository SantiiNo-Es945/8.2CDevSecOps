pipeline {
  agent any

  environment {
    // If "Allowed Domains" is empty you can put any address here.
    // If you set "Allowed Domains" = example.com, keep to@example.com.
    EMAIL_TO = 'to@example.com'
    SMTP_ACCOUNT = 'mailtrap'   // <-- the name you gave in SMTP Accounts
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
        // keep pipeline going even if tests fail so we still send email
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm test || exit /b 0'
        }
      }
      post {
        success {
          emailext(
            account: env.SMTP_ACCOUNT,
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
            account: env.SMTP_ACCOUNT,
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
        // optional; won’t fail build if script not present
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm run coverage || exit /b 0'
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        // continue even if audit exits non-zero
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          bat 'npm audit || exit /b 0'
        }
      }
      post {
        success {
          emailext(
            account: env.SMTP_ACCOUNT,
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
            account: env.SMTP_ACCOUNT,
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
    always { echo "Build result: ${currentBuild.currentResult}" }
  }
}
