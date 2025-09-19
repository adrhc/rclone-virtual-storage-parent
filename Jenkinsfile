pipeline {
  agent any

  options {
    skipDefaultCheckout(true)
    timestamps()
    ansiColor('xterm')
    disableConcurrentBuilds()
  }

  environment {
    PROJECT_NAME = 'rclone-virtual-storage-parent'
    REPO_DIR     = "${env.PROJECTS_ADRHC}/${env.PROJECT_NAME}"
    TARGET_DIR   = "/tmp/${env.PROJECT_NAME}"
    MAVEN_ARGS   = "-Dmaven.javadoc.skip=true -B -U -ntp"
  }

  stages {
    stage('Git pull') {
      steps {
        sh 'test -d "${REPO_DIR}" || { echo "Missing ${REPO_DIR}"; exit 1; }'
        dir("${REPO_DIR}") { sh 'git pull --ff-only' }
      }
    }

    stage('Build (mvnw)') {
      steps {
        dir("${REPO_DIR}") {
          sh '''#!/usr/bin/env bash
set -eo pipefail
[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ] || { echo "SDKMAN not found for $(whoami)"; exit 1; }
. "$HOME/.sdkman/bin/sdkman-init.sh"
sdk env install   # ensures 21.0.3-oracle (from .sdkmanrc) is installed
sdk env use       # activates java=21.0.3-oracle
./mvnw ${MAVEN_ARGS} clean package verify
'''
        }
      }
      post {
        always {
          sh '''
            ln -sfn "/tmp/${PROJECT_NAME}/surefire-reports" "$WORKSPACE/surefire-reports"
            ln -sfn "/tmp/${PROJECT_NAME}/failsafe-reports" "$WORKSPACE/failsafe-reports"
          '''          
          junit allowEmptyResults: true, testResults: 'surefire-reports/*.xml,failsafe-reports/*.xml'
        }
      }
    }
  }
}
