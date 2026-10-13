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
                    //sh 'docker image build -t oklabs/myimage:t${BUILD_NUMBER} -f Dockerfile.devopsstudy.in .'
                    sh 'docker-compose -f docker-compose.devopsstudy.yaml build devopsstudy'
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker image push oklabs/myimage:t${BUILD_NUMBER}'
                }
            }
        }
        stage('Deploy on Production Server') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'prod-server-credentials',
                        usernameVariable: 'SSH_USER',
                        passwordVariable: 'SSH_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        export SSHPASS="$SSH_PASS"

                        sshpass -e ssh \
                            -o StrictHostKeyChecking=no \
                            -o PreferredAuthentications=password \
                            -o PubkeyAuthentication=no \
                            "$SSH_USER@103.216.171.222" \
                            "docker rm -f devopsstudy 2>/dev/null || true; docker run --name devopsstudy -d --network host --restart unless-stopped oklabs/myimage:t${BUILD_NUMBER}"
                    '''
                }
            }
        }

    }
}
