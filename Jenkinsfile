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
                    sh 'docker rmi -f sudokudep || true'
                    echo 'Clean cache'
                    sh 'docker build -t sudokudep:latest -f DockerFileDep .'
                }
            }
      }
        stage('Build') {
            steps {
                nodejs('Node20'){
                    sh 'docker rmi -f sudokubuild || true'
                    sh 'docker volume create vol_wejsciowy'
                    sh 'docker build -t sudokubuild -f DockerFileBuild .'
                    sh 'docker run --mount type=volume,src="vol_wejsciowy",dst=/sudoku_1/deploy sudokubuild:latest bash -c "ls -l && cd .. && cp -r /sudoku/sudoku_1/deploy"'
                    
                }
            }
        }
        stage('Run tests') {
            steps {
                sh 'docker rmi -f sudokutest || true'
                sh 'docker build -t sudokutest -f DockerFileTest .'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker rm -f sudokudeploy || true'
		        sh 'docker run -dit --name sudokudeploy --mount type=volume,src="vol_wejsciowy",dst=/dest node:latest'
                sh 'docker container exec sudokudeploy sh -c "ls -l && cd dest && ls -l && cd sudoku && ls -l"'
                //sh 'docker container exec sudokudeploy sh -c "cd dest/sudoku && ls -l && yarn start"'
                //sh 'docker container kill sudokudeploy'
            }
        }
    }
}
