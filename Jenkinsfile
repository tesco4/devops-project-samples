
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

  post {
    always {
      archiveArtifacts artifacts: 'dist/**', fingerprint: true
    }
  }
}

    stage('Deploy') {
      steps {
        sh '''
          set -e
          rm -rf /opt/prod-server/*
          tar -xzf dist/webapp.tgz -C /opt/prod-server
          echo "Deployed to /opt/prod-server:"
          ls -la /opt/prod-server
        '''
      }
    }
