pipeline{
    agent any
    tools {
      maven 'mav'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/RicksConsolidatedRepo/XYZ_LTD'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Container Build'){
            steps{
                sh "docker build . -t rjedgemon/xyz_ltd:${DOCKER_TAG} "
            }
        }
        
        stage('Container Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u rjedgemon -p ${dockerHubPwd}"
                }
                
                sh "docker push rjedgemon/xyz_ltd:${DOCKER_TAG} "
            }
        }
        
        stage('Container Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
