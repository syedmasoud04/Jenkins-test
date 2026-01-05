pipeline {
    agent any

    environment {
        VENV = ".venv"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                python3 -m venv $VENV
                source $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                source $VENV/bin/activate
                pytest tests/ --junitxml=test-results.xml
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                source $VENV/bin/activate
                flake8 app/
                '''
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                source $VENV/bin/activate
                bandit -r app/ -f txt -o bandit-report.txt
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results.xml'
            archiveArtifacts artifacts: '*.txt', fingerprint: true
        }
    }
}
