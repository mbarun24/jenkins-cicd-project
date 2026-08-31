pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Running basic test..."'
                sh 'test -f index.html'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t jenkins-cicd-app .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f jenkins-cicd-app || true
                docker run -d \
                  --name jenkins-cicd-app \
                  -p 80:80 \
                  jenkins-cicd-app
                '''
            }
        }
    }
}
