pipeline {
  agent any
  options { timestamps() }

  tools { nodejs 'NODE18' }

  triggers { pollSCM('* * * * *') } 

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Verify Node & npm') {
      steps { sh 'node --version && npm --version' }
    }
    stage('Install dependencies') {
      steps { sh 'npm install' } 
    stage('Run tests (optional)') {
      steps { sh 'npm test || true' } 
    }
    stage('Coverage (optional)') {
      steps { sh 'npm run coverage || true' } 
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
      archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json,coverage/**,**/lcov.info', allowEmptyArchive: true
    }
  }
}
