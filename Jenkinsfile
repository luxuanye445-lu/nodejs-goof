pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('mail smoke test') {
      steps {
        script {
          echo "About to send a test mail at ${new Date()}"
        }
        emailext(
          subject: "Jenkins mail smoke test #${env.BUILD_NUMBER}",
          to: "luxuanye445@gmail.com",        // ← 换成你的收件邮箱（可多个，用逗号分隔）
          body: """<p>Hi, this is a minimal test.</p>
                   <p>Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}</p>
                   <p>Console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>""",
          mimeType: 'text/html'
        )
      }
    }
  }
}

