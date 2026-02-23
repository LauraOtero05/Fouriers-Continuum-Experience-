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

        stage('Build Frontend') {
            steps {
                dir('Front-End') {
                    sg 'npm ci'
                    sh 'ng build --prod'
                    /*sh 'npm install --ignore-scripts'
                    sh 'npm run build'*/
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

        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }




        }
    }
