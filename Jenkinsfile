node {
  stage('SCM') {
    checkout scm
  }

  stage('Build') {
    sh 'mvn clean package'
  }
  
  stage('SonarQube Analysis') {
    def scannerHome = tool 'SonarScanner';
    withSonarQubeEnv() {
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
