pipeline {
    agent any

    environment {
        IMAGE_NAME = 'flask-devops-demo'
    }

    stages {
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install -r requirements-dev.txt
                    flake8 app.py tests --max-line-length=100
                    pytest
                '''
            }
        }

        stage('Build image') {
            steps {
                sh 'docker build --build-arg APP_VERSION=2.0.${BUILD_NUMBER} -t ${IMAGE_NAME}:jenkins-${BUILD_NUMBER} .'
            }
        }

        stage('Push to GHCR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ghcr-credentials', usernameVariable: 'GHCR_USER', passwordVariable: 'GHCR_TOKEN')]) {
                    sh '''
                        echo "$GHCR_TOKEN" | docker login ghcr.io -u "$GHCR_USER" --password-stdin
                        docker tag ${IMAGE_NAME}:jenkins-${BUILD_NUMBER} ghcr.io/muneeb4110/${IMAGE_NAME}:jenkins-${BUILD_NUMBER}
                        docker push ghcr.io/muneeb4110/${IMAGE_NAME}:jenkins-${BUILD_NUMBER}
                        docker logout ghcr.io
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl get nodes
                        kubectl -n devops-demo set image deployment/flask-demo flask-demo=ghcr.io/muneeb4110/${IMAGE_NAME}:jenkins-${BUILD_NUMBER}
                        kubectl -n devops-demo rollout status deployment/flask-demo --timeout=300s
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
