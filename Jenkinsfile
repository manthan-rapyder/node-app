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
			sh "docker push manthanrapyder/nodeappdemo:${DOCKER_TAG}"
		}
            }
        }
	stage('Change Files'){
            steps{
                sh "chmod +x changeTag.sh"
		sh "./changeTag.sh ${DOCKER_TAG}"
                }
            }
	stage('K8s Check'){
		steps{
		sh "sudo kubectl get nodes"
		sh "sudo kubectl get pods --all-namespaces"
		}    
	}
		        
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
