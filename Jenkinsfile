pipeline {
    agent any
    
    triggers {
        githubPush()
    }

    environment {
        DEPLOY_DIR = '/opt/stop'
        
        UI_REPO = 'https://github.com/io-hog-riders/stop-front.git'
        SERVER_REPO = 'https://github.com/io-hog-riders/stop-back.git'
        DEVOPS_REPO = 'https://github.com/io-hog-riders/stop-devops.git'
        
        // Database credentials (configure in Jenkins)
        DB_USER = credentials('db-user')
        DB_PASSWORD = credentials('db-password')
        
        SERVER_PORT = '3000'
        UI_PORT = '8080'
    }
    
    stages {
        stage('Clone/Update Repositories') {
            steps {
                sh """
                    mkdir -p ${DEPLOY_DIR}
                    cd ${DEPLOY_DIR}
                    
                    # Update UI
                    if [ -d "ui" ]; then
                        cd ui && git pull origin main && cd ..
                    else
                        git clone ${UI_REPO} ui
                    fi
                    
                    # Update Server
                    if [ -d "server" ]; then
                        cd server && git pull origin main && cd ..
                    else
                        git clone ${SERVER_REPO} server
                    fi
                    
                    # Update DevOps
                    if [ -d "devops" ]; then
                        cd devops && git pull origin main && cd ..
                    else
                        git clone ${DEVOPS_REPO} devops
                    fi
                """
            }
        }
        
        stage('Create Environment File') {
            steps {
                sh """
                    cat > ${DEPLOY_DIR}/devops/.env << 'EOF'
DB_USER=${DB_USER}
DB_PASSWORD=${DB_PASSWORD}
SERVER_PORT=${SERVER_PORT}
UI_PORT=${UI_PORT}
SERVER_DIR=../server
UI_DIR=../ui
EOF
                """
            }
        }
        
        stage('Deploy') {
            steps {
                dir("${DEPLOY_DIR}/devops") {
                    sh """
                        # Build and start all services
                        docker compose up -d --build
                        
                        # Wait for services to start
                        sleep 15
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo """
                ✅ Deployment successful!
                Application URL: http://localhost
                API Docs: http://localhost/api/docs
            """
        }
        failure {
            echo "❌ Deployment failed!"
            sh """
                cd ${DEPLOY_DIR}/devops
                docker compose logs --tail=50
            """
        }
    }
}