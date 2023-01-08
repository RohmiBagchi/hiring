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
                sh "docker build -t rohmi/hiring:0.0.2 ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh "docker login -u rohmi -p ${hubPwd}"
                    sh "docker push rohmi/hiring:0.0.2"
                }
            }
       	}
        stage('Docker Deploy') {
            steps {
                sshagent(['docker-host']) {
                    sh "ssh -o StrictHostKeyChecking=no  ec2-user@172.31.47.188 docker run -d -p 8080:8080 --name hiring rohmi/hiring:0.0.2"
                }
            }   
        }
    }
}
