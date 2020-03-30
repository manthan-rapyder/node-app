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
		stage('Deploy'){
			steps{
				sh "sudo cp /var/lib/jenkins/workspace/k8s-ci-cd-pipeline/services.yml /home/ubuntu"
				sh "sudo cp /var/lib/jenkins/workspace/k8s-ci-cd-pipeline/node-app-pod.yml /home/ubuntu"
			script{
					try{
						sh "sudo kubectl apply -f /home/ubuntu/node-app-pod.yml"
						sh "sudo kubectl apply -f /home/ubuntu/services.yml"
					}catch(error){
						"sudo kubectl apply -f /home/ubuntu/node-app-pod.yml"
						"sudo kubectl apply -f /home/ubuntu/services.yml"
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
