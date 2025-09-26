pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: 'https://github.com/SantiiNo-ES945/8.2CDevSecOps.git' }
    }
    stage('Install Dependencies') { steps { bat 'npm install' } }
    stage('Run Tests')           { steps { bat 'npm test || exit /b 0' } }
    stage('Generate Coverage Report') { steps { bat 'npm run coverage || exit /b 0' } }
    stage('NPM Audit (Security Scan)') { steps { bat 'npm audit || exit /b 0' } }
    stage('Notify') {
    steps {
        emailext(
            subject: "Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}",
            body: """<p>Build result: ${currentBuild.currentResult}</p>
                     <p>Job: ${env.JOB_NAME}</p>
                     <p>Build number: ${env.BUILD_NUMBER}</p>
                     <p>Check console: ${env.BUILD_URL}</p>""",
            to: "78b1bff03a-98fa45+user1@inbox.mailtrap.io"
        )
    }
}

  }
}
