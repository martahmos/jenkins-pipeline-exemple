# jenkins-pipeline-exemple


pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/myrepo/myapp.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            environment {
                TEST_SERVER = 'test-server'
            }
            steps {
                sshagent(['my-ssh-credentials']) {
                    sh "scp target/myapp.war ${env.TEST_SERVER}:~/"
                    sshCommand remote: "${env.TEST_SERVER}", command: "sudo systemctl restart tomcat"
                }
            }
        }
    }
}
