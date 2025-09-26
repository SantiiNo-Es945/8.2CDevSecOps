pipeline {
  agent any
  environment {
    EMAIL_TO = 'to@example.com'  // keep this if "Allowed Domains" = example.com; else any address
  }
  options { timestamps() }

  stages {
    stage('EMAIL SMOKE TEST') {
      steps {
        echo "Sending test email via email-ext..."
        emailext(
          to: env.EMAIL_TO,
          subject: "[SMOKE] Jenkins email test - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          body: """<p>This is an unconditional smoke test from stage 1.</p>
                   <p>Job: ${env.JOB_NAME} | Build: ${env.BUILD_NUMBER}</p>
                   <p>Console: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
          mimeType: 'text/html'
        )
      }
    }
  }

  post { always { echo "Done." } }
}
