pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('* * * * *') }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Verify Node & npm') {
      steps {
        sh '/opt/homebrew/bin/node -v && /opt/homebrew/bin/npm -v'
      }
    }

    stage('Install dependencies') {
      steps {
        sh '/opt/homebrew/bin/npm install'
      }
    }

    stage('Run tests (optional)') {
      steps {
        sh '/opt/homebrew/bin/npm test || true'
      }
    }

    stage('Coverage (optional)') {
      steps {
        sh '/opt/homebrew/bin/npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh '''
          set -o pipefail
          (/opt/homebrew/bin/npm audit || true) | tee npm-audit.txt
          /opt/homebrew/bin/npm audit --json > npm-audit.json || true
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json,coverage/**,**/lcov.info',
                       allowEmptyArchive: true
    }
  }
}
