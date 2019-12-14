pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '14', artifactNumToKeepStr: '20'))
  }
  tools {
    maven 'default-maven'
    jdk 'java11'
  }

  environment {
    VERSION = "2.0.$BUILD_NUMBER-SNAPSHOT"
    AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'printenv'
        sh 'java -version'
      }
    }
  }
}
