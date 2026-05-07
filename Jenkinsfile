pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'ml-pipeline-app'
        CONTAINER_NAME = 'ml-pipeline-container'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
                sh 'ls -la'
            }
        }
        
        stage('Install Python Dependencies') {
            steps {
                sh 'pip3 install pandas numpy scikit-learn joblib fastapi uvicorn'
            }
        }
        
        stage('Train Model') {
            steps {
                sh '''
                    python3 train.py
                    echo "--- Training outputs ---"
                    ls -la model.pkl
                    ls -la metrics.json
                    cat metrics.json
                '''
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }
        
        stage('Deploy Container') {
            steps {
                sh '''
                    docker stop $CONTAINER_NAME 2>/dev/null || true
                    docker rm $CONTAINER_NAME 2>/dev/null || true
                    docker run -d --name $CONTAINER_NAME -p 8001:8000 $DOCKER_IMAGE:latest
                    sleep 15
                    docker ps
                    docker logs $CONTAINER_NAME
                '''
            }
        }
        
        stage('Health Check') {
            steps {
                sh '''
                    curl -s http://localhost:8001/metrics || echo "FAILED"
                '''
            }
        }
    }
}