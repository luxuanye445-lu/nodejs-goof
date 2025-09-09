pipeline {
    agent any
    stages {
        stage('Mail Test') {
            steps {
                emailext(
                    to: 'luxuanye445@gmail.com',
                    subject: 'Jenkins 测试邮件',
                    body: '这是一封来自 Jenkins Pipeline 的测试邮件。'
                )
            }
        }
    }
}
