pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
		    sh "docker build . -t manthanrapyder/nodeappdemo:${DOCKER_TAG}"
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-pwd', variable: 'DockerHubPwd')]) {
			sh "docker login -u manthanrapyder -p ${DockerHubPwd}"
			sh "docker push manthanrapyder/${DOCKER_TAG}"
		}
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
