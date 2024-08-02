pipeline {
    agent any
    
    environment {
        GITOPS_REPO = 'https://github.com/MelekBadreddine/docker-api-gitops.git'
        GITOPS_BRANCH = 'main'
        GITHUB_CREDENTIALS = credentials('github')
    }
    
    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout GitOps Repository') {
            steps {
                git branch: "${GITOPS_BRANCH}", credentialsId: 'github', url: "${GITOPS_REPO}"
            }
        }
        
        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    sh """
                        sed -i 's|image: melekbadreddine/backend.*|image: melekbadreddine/backend:latest|' backend.yaml
                    """
                    
                    sh """
                        sed -i 's|image: melekbadreddine/frontend.*|image: melekbadreddine/frontend:latest|' frontend.yaml
                    """
                }
            }
        }
        
        stage('Commit and Push Changes') {
            steps {
                script {
                    sh """
                        git config user.name "MelekBadreddine"
                        git config user.email "mbadreddine5@gmail.com"
                        git add .
                        git commit -m "Update image tags to latest"
                    """
                    
                    withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/MelekBadreddine/docker-api-gitops.git ${GITOPS_BRANCH}"
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
