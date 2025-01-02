pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    environment {
        NODEJS_HOME = 'C:\Program Files\nodejs'
        SONAR_SCANNER_PATH = 'C:\sonarqube\sonar-scanner-6.2.1.4610-windows-x64\bin'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm ci 
                '''
            }
        }

        stage('Debug ESLint Plugins') {
            steps {
                bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm list eslint-plugin-react || echo "eslint-plugin-react not found"
                '''
            }
        }

        stage('Lint') {
            steps {
                bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token')
            }
            steps {
                bat '''
                    set PATH=%SONAR_SCANNER_PATH%;%PATH%
                    where sonar-scanner || echo "SonarQube scanner not found. Please install it."
                    sonar-scanner -Dsonar.projectKey=backend_sonar ^
                    -Dsonar.sources=. ^
                    -Dsonar.host.url=http://localhost:9000 ^
                    -Dsonar.token=%SONAR_TOKEN% 2>&1
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed. Please check the logs for more details.'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
