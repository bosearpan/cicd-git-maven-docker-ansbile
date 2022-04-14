pipeline{
    agent any
    
    environment {
        DOCKER_TAG = getVersion()
    }
    stages{
        stage("SCM checkout"){
            steps{
                git branch: 'main', credentialsId: 'gitHub', url: 'https://github.com/bosearpan/cicd-git-maven-docker-ansbile.git'  
                }
            }
        stage("Maven build"){
            steps{
                sh "mvn clean package"
                }
            }
        stage("Dockerize"){
            steps{
                sh "docker build . -t arpanboss/javaapp:${DOCKER_TAG}"
                }
            }
        stage("DockerHub push"){
            steps{
                withCredentials([string(credentialsId: 'dockerHub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u arpanboss -p ${dockerHubPwd}"
                }
                sh "docker push arpanboss/javaapp:${DOCKER_TAG}"
                }
            }
        stage("Deploy container on dev server"){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'host.inv', playbook: 'ansible-docker-play.yml'
                }
            }
            
            
    }
}
def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
    
}
