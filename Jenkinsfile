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
  }

  stages {
    stage('Git pull') {
      steps {
        sh 'test -d "${REPO_DIR}" || { echo "Missing ${REPO_DIR}"; exit 1; }'
        dir("${REPO_DIR}") { sh 'git pull --ff-only' }
      }
    }

    stage('Build') {
      steps {
        dir("${REPO_DIR}") { sh './clean.install.sh' }
      }
    }
  }
}
