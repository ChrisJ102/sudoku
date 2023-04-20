pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[url: 'https://github.com/Jarczk/example-yarn-package']]])
            }
        }
        stage('Install dependencies') {
            steps {
                nodejs('Node-18.14'){
                    echo 'Clean cache'
                    sh 'yarn cache clean'
                    sh 'yarn install'
                }
            }
        }
        stage('Run tests') {
            steps {
                nodejs('Node-18.14'){
                sh 'yarn test'
                }
            }
        }
    }
}
