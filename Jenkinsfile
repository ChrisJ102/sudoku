pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[url: 'https://github.com/ChrisJ102/sudoku']]])
            }
        }
      stage('Install Dependencies'){
        steps {
                nodejs('Node20'){
                    echo 'Clean cache'
                    sh 'docker build -t sudokuDep:latest -f DockerFileDep .'
                }
            }
      }
        stage('Build') {
            steps {
                nodejs('Node20'){
                    sh 'docker build -t sudokuBuild -f DockerFileBuild .'
                }
            }
        }
        stage('Run tests') {
            steps {
                sh 'docker build -t sudokuTest -f DockerFileTest .'
            }
        }
    }
}
