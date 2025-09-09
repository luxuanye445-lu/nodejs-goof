pipeline {
  agent any

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  // 每分钟检查一次 Git 变化
  triggers { pollSCM('* * * * *') }

  // Mac 本机 Homebrew Node 路径
  environment {
    PATH = "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    // 证明是自动触发
    stage('Auto Trigger Proof') {
      steps { sh 'echo "✅ Auto triggered at $(date)"' }
    }

    stage('Verify Node & npm') {
      steps { sh 'which node && node -v && which npm && npm -v' }
    }

    stage('Install dependencies') {
      steps { sh 'if [ -f package-lock.json ]; then npm ci; else npm install; fi' }
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

    // 从 npm-audit.json 生成简短摘要，便于邮件正文展示
    stage('Build Audit Summary') {
      steps {
        sh '''
          node -e "try{const d=require('./npm-audit.json');const v=d.metadata?.vulnerabilities||{}; console.log(`low:${v.low||0}, moderate:${v.moderate||0}, high:${v.high||0}, critical:${v.critical||0}`)}catch(e){console.log('no-audit-json')}" \
          | tee audit-summary.txt
        '''
      }
    }
  }

  post {
    always {
      // 归档到本次构建产物，方便网页查看/下载
      archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json,audit-summary.txt', allowEmptyArchive: true
    }

    success {
      script {
        def summary = fileExists('audit-summary.txt') ? readFile('audit-summary.txt').trim() : 'summary unavailable'
        emailext(
          to: 'luxuanye445@gmail.com',                // ← 改成你的收件邮箱（可逗号分隔多个）
          subject: "[SUCCESS] ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          body: """<p>✅ Build succeeded.</p>
                   <p><b>Audit summary:</b> ${summary}</p>
                   <p>Artifacts: npm-audit.txt / npm-audit.json / audit-summary.txt</p>
                   <p>Job: <a href="${env.BUILD_URL}">${env.JOB_NAME} #${env.BUILD_NUMBER}</a></p>
                   <p>Console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>""",
          mimeType: 'text/html',
          attachmentsPattern: 'npm-audit.txt,npm-audit.json,audit-summary.txt'
        )
      }
    }

    failure {
      script {
        def summary = fileExists('audit-summary.txt') ? readFile('audit-summary.txt').trim() : 'summary unavailable'
        emailext(
          to: 'luxuanye445@gmail.com',                // ← 改成你的收件邮箱
          subject: "[FAILURE] ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          body: """<p>❌ Build failed.</p>
                   <p><b>Audit summary (may be partial):</b> ${summary}</p>
                   <p>Check console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>""",
          mimeType: 'text/html',
          attachmentsPattern: 'npm-audit.txt,npm-audit.json,audit-summary.txt'
        )
      }
    }
  }
}
