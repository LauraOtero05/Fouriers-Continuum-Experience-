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

        stage('Compile Backend') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean package -DskipTests -B'
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
                        docker build -t nexus:5000/backend:latest .
                        echo $DOCKER_PASS | docker login nexus:5000 -u $DOCKER_USER --password-stdin
                        docker push nexus:5000/backend:latest
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
                        docker build -t nexus:5000/frontend:latest .
                        echo $DOCKER_PASS | docker login nexus:5000 -u $DOCKER_USER --password-stdin
                        docker push nexus:5000/frontend:latest
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

