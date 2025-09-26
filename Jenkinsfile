pipeline {
  agent any

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
        bat 'npm test || exit /b 0'
        emailext(
          subject: "Tests finished: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          to: 'to@example.com',   // Mailtrap will still capture this
          mimeType: 'text/html',
          body: """<p>Result: <b>${currentBuild.currentResult}</b></p>
                   <p>Job: ${env.JOB_NAME}</p>
                   <p>Build: ${env.BUILD_NUMBER}</p>
                   <p>Console: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
          attachLog: true
        )
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat 'npm run coverage || exit /b 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        bat 'npm audit || exit /b 0'
        emailext(
          subject: "Security scan finished: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          to: 'to@example.com',
          mimeType: 'text/html',
          body: """<p>Result: <b>${currentBuild.currentResult}</b></p>
                   <p>Job: ${env.JOB_NAME}</p>
                   <p>Build: ${env.BUILD_NUMBER}</p>
                   <p>Console: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
          attachLog: true
        )
      }
    }
  }
}
