pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build . -t kammana/hiring:${env.BUILD_NUMBER}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh "docker login -u rohmi -p ${hubPwd}"
                    sh "docker push rohmi/hiring:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                sshagent(['docker-host']) {
                    sh "ssh -o StrictHostKeyChecking=no  ec2-user@172.31.47.188 docker rm -f hiring"
                    sh "ssh  ec2-user@172.31.47.188 docker run -d -p 8080:8080 --name hiring rohmi/hiring:${env.BUILD_NUMBER}"
                }
            }
        }

    }
}

def commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse HEAD'
    return id
}
