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
        stage('Install dependencies') {
		steps{
			script {
				sh 'docker rmi -f sudokudepen || true'
				echo 'pre-docker'
				sh 'docker build --no-cache -t sudokudepen:latest . -f DocerfileInstall'
				echo 'Dependecies were successfully installed.'
			}
		}
        }
        stage('Build') {
            steps {
                script {
                    sh 'docker rmi -f sudokubuild || true'
                    sh 'docker volume create volume_wej'
                    sh 'docker build --no-cache -t sudokubuild:latest . -f DockerfileBuild'
                    sh 'docker run --mount type=volume,src="volume_wej",dst=wejscie sudokubuild:latest'
                    
                    echo 'Build was successfully completed.'
		}
            }
        }
    }
}
