pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'ml-pipeline-app'
        CONTAINER_NAME = 'ml-pipeline-container'
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Arhamhir/lab-mid.git'
            }
        }
        
        stage('Pull Latest Data') {
            steps {
                sh '''
                    git pull origin main
                '''
            }
        }
        
        stage('Fetch Data from GitHub') {
            steps {
                sh '''
                    echo "Dataset already fetched via git clone"
                    ls -la dataset/
                '''
            }
        }
        
        stage('Train Model') {
            steps {
                sh '''
                    python3 train.py
                '''
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $DOCKER_IMAGE:latest .
                '''
            }
        }
        
        stage('Stop Existing Container') {
            steps {
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                '''
            }
        }
        
        stage('Run Docker Container') {
            steps {
                sh '''
                    docker run -d \
                        --name $CONTAINER_NAME \
                        -p 8000:8000 \
                        $DOCKER_IMAGE:latest
                '''
            }
        }
        
        stage('Verify Deployment') {
            steps {
                sh '''
                    sleep 5
                    curl -s http://localhost:8000/metrics || echo "API not ready yet"
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo 'Pipeline succeeded! API is running at http://35.170.34.8:8000/metrics'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}