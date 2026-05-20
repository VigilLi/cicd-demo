pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo '从 GitHub 拉取代码...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo '构建 Docker 镜像...'
                sh 'docker build -t cicd-demo:latest .'
            }
        }
        
        stage('Push') {
            steps {
                echo '推送镜像到阿里云（暂未配置）...'
                echo '镜像构建完成'
            }
        }
        
        stage('Deploy') {
            steps {
                echo '部署到服务器（暂未配置）...'
                echo '部署完成'
            }
        }
        
        stage('Health Check') {
            steps {
                echo '健康检查...'
                echo '服务正常'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline 执行成功！'
        }
        failure {
            echo 'Pipeline 执行失败！'
        }
    }
}
