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

        stage('Install Frontend') {
            steps {
                dir('Front-End') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('Front-End') {
                    sh 'npm run build -- --prod'
                }
            }
        }

        stage('SonarQube Analysis') {
            tools {
                nodejs 'NodeJS18'
            }
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Compile Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean package -B'
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
