pipeline {
    agent any

    environment {
        IMAGE = "flask-hello"
        TAG   = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                git branch: 'main', url: 'https://github.com/mrushdan/flask-jenkins.git'
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker run --rm $IMAGE:$TAG python -c "
from app import app
response = app.test_client().get('/')
assert response.status_code == 200
assert b'Hello from Dockerized Flask App' in response.data
print('All tests passed')
"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f flask-app || true'
                sh 'docker run -d --name flask-app -p 80:5000 $IMAGE:$TAG'
            }
        }
    }

    post {
        success {
            echo "Deployed build ${env.BUILD_NUMBER} to port 80"
        }
        failure {
            echo "Pipeline failed, nothing was deployed"
        }
    }
}
