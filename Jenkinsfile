pipeline {
    agent any
    
    stages {
        stage('Fetch Data from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/Arhamhir/lab-mid.git'
            }
        }
        
        stage('Train Model') {
            steps {
                sh '''
                pip3 install pandas scikit-learn fastapi uvicorn --break-system-packages
                python3 train.py
                '''
            }
        }
        
        stage('Dockerize and Run API') {
            steps {
                sh '''
                docker stop ml-api || true
                docker rm ml-api || true
                docker build -t ml-api-image .
                docker run -d --name ml-api -p 8001:8000 ml-api-image
                '''
            }
        }
    }
}