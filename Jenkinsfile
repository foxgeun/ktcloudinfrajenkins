pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('parksugeun') // Jenkins에 등록된 DockerHub 인증 ID
        IMAGE_NAME = 'dockerhubid/ktcloudinfra4:0727'
    }

    stages {
        stage('Checkout') {
            steps {
                // Git 소스 코드 체크아웃
                checkout scm
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    // 1. Dockerfile과 index.html을 기반으로 이미지 생성
                    def customImage = docker.build("${env.IMAGE_NAME}")
                    
                    // 2. DockerHub 로그인 및 Push
                    docker.withRegistry('https://index.docker.io/v1/', 'parksugeun') {
                        customImage.push()
                    }
                }
            }
        }

        stage('Ansible Deploy File Copy') {
            steps {
                // 3. Ansible을 사용하여 deploy.yml을 마스터 노드의 root 홈디렉토리로 복사
                // (Ansible 플레이북 또는 ad-hoc 명령어를 Jenkins 노드에서 실행)
                sh 'ansible master -i inventory -m copy -a "src=deploy.yml dest=/root/deploy.yml"'
            }
        }

        stage('Kubernetes Apply') {
            steps {
                // 4. 마스터 노드에서 kubectl apply -f deploy.yml 실행
                sh 'ansible master -i inventory -m shell -a "kubectl apply -f /root/deploy.yml"'
            }
        }
    }
}
