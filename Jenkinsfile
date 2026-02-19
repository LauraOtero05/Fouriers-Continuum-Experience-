pipeline {
    agent any

    tools {
        maven 'Default Maven'
        jdk 'Default JDK'
        nodejs 'NodeJS10'
    }

    stages {

        stage('SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Test Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn test'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('Front-End') {
                    nodejs(nodeJSInstallationName: 'NodeJS10') {
                        sh 'npm install || true'
                        sh 'npm run build'
                    }
                }
            }
        }
        
        stage('Test Frontend') {
            steps {
                dir('Front-End') {
                    nodejs(nodeJSInstallationName: 'NodeJS10') {
                        sh 'npm install || true'
                        sh 'npm test || true'
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}
