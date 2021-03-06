def boolean hasChangesIn(String module) {
    if (env.CHANGE_TARGET == null) {
        return false;
    }

    def MASTER = sh(
        returnStdout: true,
        script: "git rev-parse origin/${env.CHANGE_TARGET}"
    ).trim()

    // Gets commit hash of HEAD commit. Jenkins will try to merge master into
    // HEAD before running checks. If this is a fast-forward merge, HEAD does
    // not change. If it is not a fast-forward merge, a new commit becomes HEAD
    // so we check for the non-master parent commit hash to get the original
    // HEAD. Jenkins does not save this hash in an environment variable.
    def HEAD = sh(
        returnStdout: true,
        script: "git show -s --no-abbrev-commit --pretty=format:%P%n%H%n HEAD | tr ' ' '\n' | grep -v ${MASTER} | head -n 1"
    ).trim()

    return sh(
        returnStatus: true,
        script: "git diff --name-only ${MASTER}...${HEAD} | grep ^${module}"
    ) == 0
}

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
        sh 'printenv | sort'
      }
    }
    stage('hasChanges')  {
      when {
        expression { return hasChangesIn('Jenkinsfile') }
      }
      steps {
        echo 'Jenkinsfile has changes'
      }
    }
    stage('changeset') {
      when {
        changeset 'Jenkinsfile'
      }
      steps {
        echo 'changeset includes Jenkinsfile'
      }
    }
    stage('either') {
      when { anyOf {
        expression { return hasChangesIn('Jenkinsfile') }
        changeset 'Jenkinsfile'
      }}
      steps {
        echo 'either'
      }
    }
    stage('both') {
      when { allOf {
        expression { return hasChangesIn('Jenkinsfile') }
        changeset 'Jenkinsfile'
      }}
      steps {
        echo 'both'
      }
    }
  }
}
