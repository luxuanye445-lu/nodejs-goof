pipeline {
  agent any

  options {
    timestamps()              
    disableConcurrentBuilds()  
  }

 
  triggers { pollSCM('* * * * *') }

  
  environment {
    PATH = "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    
    stage('Auto Trigger Proof') {
      steps { sh 'echo "Auto triggered at $(date)"' }
    }

    stage('Verify Node & npm') {
      steps { sh 'which node && node -v && which npm && npm -v' }
    }

    stage('Install dependencies') {
      steps { 
        sh 'if [ -f package-lock.json ]; then npm ci; else npm install; fi'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh '''
          set -o pipefail
          (npm audit || true) | tee npm-audit.txt
          npm audit --json > npm-audit.json || true
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json', allowEmptyArchive: true
    }

    success {
      emailext(
        subject: "[SUCCESS] ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        to: 'luxuanye445@example.com',   
        body: """<p>Build succeeded: <a href="${env.BUILD_URL}">${env.JOB_NAME} #${env.BUILD_NUMBER}</a></p>
                 <p>Console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                 <p>Attached: npm-audit.txt / npm-audit.json</p>""",
        mimeType: 'text/html',
        attachmentsPattern: 'npm-audit.txt,npm-audit.json'
      )
    }

    failure {
      emailext(
        subject: "[FAILURE] ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        to: 'luxuanye445@example.com',   
        body: """<p>Build failed: <a href="${env.BUILD_URL}">${env.JOB_NAME} #${env.BUILD_NUMBER}</a></p>
                 <p>Check console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>""",
        mimeType: 'text/html'
      )
    }
  }
}

