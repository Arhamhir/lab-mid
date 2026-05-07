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
                sh 'git log --oneline -1'
                sh 'ls -la'
            }
        }
        
        stage('Verify Dataset') {
            steps {
                sh '''
                    echo "Checking dataset..."
                    ls -la dataset/
                    wc -l dataset/train.csv
                '''
            }
        }
        
        stage('Train Model') {
            steps {
                sh '''
                    echo "Training model..."
                    python3 train.py
                    echo "Training complete!"
                '''
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t $DOCKER_IMAGE:latest .
                    echo "Docker image built successfully!"
                '''
            }
        }
        
        stage('Deploy Container') {
            steps {
                sh '''
                    echo "Stopping existing container..."
                    docker stop $CONTAINER_NAME 2>/dev/null || true
                    docker rm $CONTAINER_NAME 2>/dev/null || true
                    
                    echo "Starting new container..."
                    docker run -d \
                        --name $CONTAINER_NAME \
                        -p 8001:8000 \
                        $DOCKER_IMAGE:latest
                    
                    echo "Container started!"
                    docker ps
                '''
            }
        }
        
        stage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for API to start..."
                    sleep 10
                    
                    echo "Checking API health..."
                    curl -s http://localhost:8001/ || echo "Root endpoint failed"
                    curl -s http://localhost:8001/metrics || echo "Metrics endpoint failed"
                '''
            }
        }
    }
    
    post {
        success {
            echo '✅ PIPELINE SUCCESS!'
            echo '🌐 API URL: http://35.170.34.8:8001/metrics'
        }
        failure {
            echo '❌ PIPELINE FAILED!'
            echo 'Check logs above for errors'
        }
        always {
            echo 'Pipeline execution completed'
        }
    }
}