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
	stage('K8s Deployment'){
            steps{
		sh "git clone https://github.com/manthan-rapyder/node-app.git"
		sh "chmod +x changeTag.sh"
		sh "./changeTag.sh ${DOCKER_TAG}"
		sh "cp /var/lib/jenkins/workspace/k8s-ci-cd-pipeline/pods.yml ~/"
		sh "cp /var/lib/jenkins/workspace/k8s-ci-cd-pipeline/node-app-pod.yml ~/"
		sh "kubectl apply -f ."
		sh "kubectl create -f ."
                }
            }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
