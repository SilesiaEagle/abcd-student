pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: '236c5fa7-2164-4a22-bdeb-3abd1be1d7ed', url: 'https://github.com/SilesiaEagle/abcd-student', branch: 'main'
                }
            }
        }
        stage('Welcome message') {
            steps {
                echo 'Hello Maciek!'
            }
        }

        stage('Prepare') {
            steps {
                sh 'mkdir -p results'
            }
        }

        stage ('DAST') {
            steps {
                sh 'docker run --name juice-shop -d --rm -p 3000:3000 bkimminich/juice-shop'
            }
        }
    }
}
