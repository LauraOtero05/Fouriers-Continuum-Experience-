pipeline {
    agent any

    tools {
        maven 'Default Maven'
        jdk 'Default JDK'
        nodejs 'NodeJS10'
        dockerTool 'FCE Docker'
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
                    sh 'npm install --ignore-scripts'
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

        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Compile Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean package -DskipTests -B'
                }
            }
        }

        stage('Integration Tests - Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean verify'
                }
            }
        }

        stage('Integration Tests - Frontend') {
            steps {
                dir('Front-End') {
                    sh 'npm run e2e'
                }
            }
        }

        //ID_nexus_docker

        stage('Docker Build & Push to Nexus') {
            steps {
                script {
                    def nexusRegistry = "host.docker.internal:5000"
                    def backImageName = "${nexusRegistry}/its-backend"
                    def frontImageName = "${nexusRegistry}/its-frontend"
                    withEnv(["NO_PROXY=host.docker.internal,nexus,127.0.0.1,localhost"]) {
                        docker.withRegistry("http://${nexusRegistry}", 'ID_nexus_docker') {
                            dir('Back-End') {
                                def backImage = docker.build("${backImageName}:${env.BUILD_NUMBER}")
                                backImage.push()
                                backImage.push("latest")
                            }
                    
                            dir('Front-End') {
                                def frontImage = docker.build("${frontImageName}:${env.BUILD_NUMBER}")
                                frontImage.push()
                                frontImage.push("latest")
                            }
                        }
                    }
                }
            }
        }
        
    }
}
