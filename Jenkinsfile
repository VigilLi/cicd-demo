pipeline {
    agent any
    
    environment {
        VERSION = sh(script: "date +%Y%m%d%H%M%S", returnStdout: true).trim()
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '拉取代码...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "构建镜像: cicd-demo:${VERSION}"
                sh "docker build -t cicd-demo:${VERSION} ."
                sh "docker tag cicd-demo:${VERSION} cicd-demo:latest"
            }
        }
        
        stage('Deploy') {
            steps {
                echo '部署到 K3s...'
                sh '''
                    sudo kubectl set image deployment/cicd-demo nginx=cicd-demo:latest --record
                    sudo kubectl rollout status deployment/cicd-demo --timeout=60s
                '''
            }
        }
        
        stage('Health Check') {
            steps {
                echo '健康检查...'
                sh 'curl -f http://localhost:30080 || exit 1'
            }
        }
    }
    
    post {
        success {
            echo "✅ 部署成功！版本: ${VERSION}"
        }
        failure {
            echo "❌ 部署失败，自动回滚..."
            sh 'sudo kubectl rollout undo deployment/cicd-demo'
        }
    }
}
