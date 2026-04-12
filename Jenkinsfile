pipeline {
    agent any
    
    environment {
        PYTHON_VERSION = '3.9'
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                checkout scm
                bat 'git log --oneline -1'
            }
        }
        
        stage('Setup Python') {
            steps {
                echo "Setting up Python environment..."
                bat 'python --version'
                bat 'pip3 --version'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo "Installing Python dependencies..."
                bat 'pip3 install -r requirements.txt'
            }
        }
        
        
        stage('Run Application') {
            steps {
                echo "Running the application..."
                bat 'python app.py'
            }
        }
        
        stage('Run Tests') {
            steps {
                echo "Running unit tests..."
                bat 'python -m pytest test_app.py -v --tb=batort'
            }
        }
        
        stage('Test Coverage') {
            steps {
                echo "Generating test coverage report..."
                bat 'python -m pytest test_app.py --cov=app --cov-report=term-missing || true'
            }
        }
        
        stage('Build Artifacts') {
            steps {
                echo "Creating build artifacts..."
                bat '''
                    mkdir -p dist
                    cp app.py dist/
                    cp test_app.py dist/
                    cp requirements.txt dist/
                    echo "Artifacts ready in dist/ directory"
                '''
            }
        }
    }
    
    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed! Check logs above."
        }
    }
}
