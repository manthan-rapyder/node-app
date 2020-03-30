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
		sshagent(['k8s-master']) {
			sh "scp -o StrictHostKeyChecking=no node-app-pod.yml services.yml admin@54.175.251.189:/home/admin/"
			script{
					try{
					sh "ssh admin@54.175.251.189 kubectl apply -f ."
					}catch(error){
					sh "ssh admin@54.175.251.189 kubectl create -f ."
					}
				}
			}
		}
	}
		        
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
