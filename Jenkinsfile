
pipeline {
  agent any

  environment {
    APP_DIR = 'node/plain/webappWithTests/Application'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install') {
      steps {
        dir("${APP_DIR}") {
          sh '''
            set -e
            node -v
            npm -v
            npm ci
          '''
        }
      }
    }

    stage('Test') {
      steps {
        dir("${APP_DIR}") {
          sh '''
            set -e
            echo "No tests configured in package.json (no scripts section). Skipping tests."
          '''
        }
      }
    }

    stage('Package') {
      steps {
        sh '''
          set -e
          rm -rf dist
          mkdir -p dist
          tar -czf dist/webapp.tgz -C "${APP_DIR}" .
          ls -la dist
        '''
      }
    }
  }

  stage('Deploy') {
      steps {
        sh '''
          set -e
          mkdir -p "${DEPLOY_DIR}"
          rm -rf "${DEPLOY_DIR:?}/"*
          tar -xzf dist/webapp.tgz -C "${DEPLOY_DIR}"
          echo "Deployed to ${DEPLOY_DIR}"
          ls -la "${DEPLOY_DIR}"
        '''
      }
    }
  }
  
  post {
    always {
      archiveArtifacts artifacts: 'dist/**', fingerprint: true
    }
  }
}

