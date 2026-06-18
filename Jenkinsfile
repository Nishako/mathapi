pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'qa', url: 'https://github.com/Nishako/mathapi.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan . --format HTML --format XML', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'npx sonar-scanner -Dsonar.projectKey=mathapi -Dsonar.sources=src'
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh 'docker stop mathapi-qa || true'
                sh 'docker rm mathapi-qa || true'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t mathapi-qa .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d --name mathapi-qa -p 3001:3000 mathapi-qa'
            }
        }

        stage('Logs') {
            steps {
                sh 'sleep 5'
                sh 'docker logs mathapi-qa'
            }
        }
    }
}