pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://nexus:8081'
    }

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
                    sh 'mvn clean package -DskipTests -B'
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                dir('Back-End') {
                    withCredentials([usernamePassword(credentialsId: 'ID_nexus', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                        configFileProvider([configFile(fileId: 'maven-settings-nexus', variable: 'MAVEN_SETTINGS')]) {
                    sh """
                        mvn clean deploy -DskipTests -s $MAVEN_SETTINGS -B
                    """
                }
            }
        }
    }
}

        //ID_nexus_docker

stage('Build Docker Backend') {
    steps {
        dir('Back-End') {
            withCredentials([usernamePassword(credentialsId: 'ID_nexus_docker', 
                usernameVariable: 'DOCKER_USER', 
                passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        docker build -t my-nexus-host/backend:latest .
                        echo $DOCKER_PASS | docker login my-nexus-host -u $DOCKER_USER --password-stdin
                        docker push my-nexus-host/backend:latest
                    '''
                }
        }
    }
}

stage('Build Docker Frontend') {
    steps {
        dir('Front-End') {
            withCredentials([usernamePassword(credentialsId: 'ID_nexus_docker', 
                usernameVariable: 'DOCKER_USER', 
                passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        docker build -t my-nexus-host/frontend:latest .
                        echo $DOCKER_PASS | docker login my-nexus-host -u $DOCKER_USER --password-stdin
                        docker push my-nexus-host/frontend:latest
                    '''
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

