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
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS_ID}") {
                        def customImage = docker.build("${IMAGE_NAME}")
                        customImage.push()
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
