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
