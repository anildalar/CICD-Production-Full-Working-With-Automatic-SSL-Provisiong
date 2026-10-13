pipeline{
    agent any
    stages{
        stage(""){
            steps{
            sh '''
                    apt update -y
                    apt upgrade -y
                    apt install vim sshpass sudo docker.io docker-compose -y
                    service docker start
                    service docker status
                '''
            }
        }
        stage("Image build and Push"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'docker image build -t oklabs/myimage:t${BUILD_NUMBER} -f Dockerfile.devopsstudy.in .'
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker image push oklabs/myimage:t${BUILD_NUMBER}'
                }
            }
        }
        stage('Deploy on Production Server') {
            steps {
                sshagent(credentials: ['prod']) {
                    sh 'ssh -o StrictHostKeyChecking=no -o IdentitiesOnly=yes root@103.216.171.222 "docker ps -a"'
                }
            }
        }

    }
}
