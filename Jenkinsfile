pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('* * * * *') }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Verify Node & npm') {
      steps { sh 'node -v && npm -v' }
    }

    stage('Install dependencies') {
      steps { sh 'npm install' }
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
  }
}

