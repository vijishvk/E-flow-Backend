pipeline {
    agent any
    environment {
        IMAGE_NAME = 'app-backend'
        CONTAINER_NAME = 'backend-contain'
        EC2_IP = '100.53.248.149'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'git_creds',
                    url: 'https://github.com/vijishvk/E-flow-Backend.git'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2_creds']) {
                    sh '''
                        ssh -A -o StrictHostKeyChecking=no ubuntu@${EC2_IP} "set -e && \
                            rm -rf /home/ubuntu/backend && \
                            mkdir -p /home/ubuntu/backend && \
                            cd /home/ubuntu/backend && \
                            git clone https://github.com/vijishvk/E-flow-Backend.git && \
                            cd /home/ubuntu/backend/E-flow-Backend && \
                            (docker ps -q --filter publish=3000 | xargs -r docker stop) && \
                            (docker ps -aq --filter publish=3000 | xargs -r docker rm) && \
                            (docker rmi -f app-backend || true) && \
                            docker build -t app-backend . && \
                            docker run -d -p 3000:3000 --name backend-contain app-backend"
                    '''
                }
            }
        }
    }
    post {
        success {
            echo "Backend deployed successfully"
        }
        failure {
            echo "Failed"
        }
    }
}
