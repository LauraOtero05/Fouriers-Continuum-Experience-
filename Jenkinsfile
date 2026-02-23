pipeline {
    agent any

    tools {
        maven 'Default Maven'
        jdk 'Default JDK'
        nodejs 'NodeJS10'
        nodejs 'NodeJS18'
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

                stage('Install Frontend') {
            steps {
                dir('Front-End') {
                    sh 'nvm use NodeJS10'
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('Front-End') {
                    sh 'nvm use NodeJS10'
                    sh 'npm run build -- --prod'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh 'nvm use NodeJS18'
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

/*
        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
*/



        }
    }
