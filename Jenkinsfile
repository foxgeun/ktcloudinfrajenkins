pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-cred'
        IMAGE_NAME = 'parksugeun/ktcloudinfra4:0727'
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Jenkins 크리덴셜 ID를 이용해 Docker Hub에 안전하게 로그인 후 빌드 및 푸시 수행
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS_ID}") {
                        sh "docker build -t ${IMAGE_NAME} ."
                        sh "docker push ${IMAGE_NAME}"
                    }
                }
            }
        }
        stage('Copy deploy.yml via Ansible') {
            steps {
                sh "ansible master -m copy -a 'src=deploy.yml dest=/root/deploy.yml'"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh "ansible master -m shell -a 'kubectl --kubeconfig=/etc/kubernetes/admin.conf apply -f /root/deploy.yml'"
            }
        }
    }
}
