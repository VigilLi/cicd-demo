pipeline {
    agent any
    
    environment {
        // 版本号：使用构建序号
        VERSION = "v${BUILD_NUMBER}"
        // 备份版本文件路径
        BACKUP_FILE = "/tmp/current_version.txt"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '从 GitHub 拉取代码...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "构建 Docker 镜像: cicd-demo:${VERSION}"
                sh "docker build -t cicd-demo:${VERSION} ."
            }
        }
        
        stage('Push') {
            steps {
                echo "推送镜像到阿里云（暂未配置）..."
                echo "镜像 cicd-demo:${VERSION} 构建完成"
            }
        }
        
        stage('Backup Current Version') {
            steps {
                echo '备份当前版本...'
                script {
                    // 如果备份文件存在，读取当前版本
                    if (fileExists(BACKUP_FILE)) {
                        def currentVersion = readFile(BACKUP_FILE).trim()
                        echo "当前版本: ${currentVersion}"
                    } else {
                        echo "首次部署，无备份"
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo "部署版本: ${VERSION}"
                // TODO: 改成 kubectl apply（等安装 k3s 后）
                sh "echo '模拟部署: docker run -d --name cicd-demo -p 8081:80 cicd-demo:${VERSION}'"
            }
        }
        
        stage('Health Check') {
            steps {
                echo '健康检查...'
                script {
                    // 模拟健康检查（后面改成真正的 curl 探测）
                    def healthCheckPassed = true
                    
                    if (healthCheckPassed) {
                        echo '✅ 健康检查通过'
                        // 保存当前版本为备份
                        writeFile file: BACKUP_FILE, text: VERSION
                    } else {
                        error('❌ 健康检查失败，触发回滚')
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Pipeline 执行成功！部署版本: ${VERSION}"
        }
        failure {
            echo "❌ Pipeline 执行失败！开始回滚..."
            script {
                // 读取备份版本
                if (fileExists(BACKUP_FILE)) {
                    def rollbackVersion = readFile(BACKUP_FILE).trim()
                    echo "回滚到版本: ${rollbackVersion}"
                    // TODO: 改成 kubectl rollout undo
                    sh "echo '模拟回滚: 恢复版本 ${rollbackVersion}'"
                } else {
                    echo "无可用备份，回滚失败"
                }
            }
        }
    }
}
