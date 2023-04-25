pipeline {

	parameters
    {
        	string(name: 'VERSION', defaultValue: '1.0.0', description: '')
        	booleanParam(name: 'PROMOTE', defaultValue: true, description: '')
    }
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
                    sh 'docker volume create vol_wyjsciowy'
                    sh 'docker build -t sudokubuild -f DockerFileBuild .'
                    //sh 'docker run --mount type=volume,src="vol_wejsciowy",dst=/sudoku_1/deploy sudokubuild bash -c "ls -l && cd .. && cp -r /sudoku /sudoku_1/deploy"'
                    docker run --mount type=volume,src="vol_wejsciowy",dst=/sudoku_1/deploy sudokubuild:latest bash -c "cd .. &&  cp -r /sudoku /sudoku_1/deploy && cp -r vol_wejsciowy/ vol_wyjsciowy/ && ls ./vol_wejsciowy && ls ./vol_wyjsciowy" 
                    
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
		        sh 'docker run -dit --name sudokudeploy --mount type=volume,src="vol_wyjsciowy",dst=/dest node:latest'
                sh 'docker container exec sudokudeploy sh -c "ls -l && cd dest && ls -l && cd sudoku && ls -l"'
                //sh 'docker container exec sudokudeploy sh -c "cd dest/sudoku && ls -l && yarn start"'
                //sh 'docker container kill sudokudeploy'
            }
        }
	stage ('Publish'){
            when
            {
                expression {return params.PROMOTE}
            }
            steps
            {
              sh 'rm -rf /var/jenkins_home/workspace/sudoku_publish'
              sh 'mkdir /var/jenkins_home/workspace/sudoku_publish'
              sh 'chmod -R 777 /var/jenkins_home/workspace/sudoku_publish'
              sh 'docker rm -f pub || true'
              sh 'docker run -d --name sudoku_pub --mount type=volume,src="vol_wyjsciowy",dst=/usr/local/sudoku_pub --mount type=bind,source=/var/jenkins_home/workspace/sudoku_publish,target=/usr/local/pub_copy node bash -c "chmod -R 777 /usr/local/sudoku_pub && chmod -R 777 /var/jenkins_home/workspace/sudoku_publish && cp -R /usr/local/sudoku_pub/. /usr/local/pub_copy"'
                sh "tar -zcvf cmake_${params.VERSION}.tar.xz -C /var/jenkins_home/workspace/sudoku_publish ."
                archiveArtifacts artifacts: "cmake_${params.VERSION}.tar.xz"
                
              sh ''' echo "Publish done" '''               
            }
        }
    }
}
