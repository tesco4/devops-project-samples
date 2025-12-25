pipeline {
  agent any

  environment {
    APP_DIR    = 'node/plain/webappWithTests/Application'
    DEPLOY_DIR = '/opt/prod-server'
    APP_PORT   = '8092'
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

    stage('Run') {
      steps {
        sh '''
          set -e
          cd "${DEPLOY_DIR}"

          # Stop previous run (if any)
          pkill -f "node server.js" || true

          # Start the app in background on APP_PORT
          nohup env PORT="${APP_PORT}" node server.js > app.log 2>&1 &

          sleep 2
          echo "App started on port ${APP_PORT}. Last log lines:"
          tail -n 20 app.log || true
        '''
      }
    }

    stage('Health Check') {
      steps {
        sh '''
          set -e
          curl -sSf "http://localhost:${APP_PORT}/" >/dev/null
          echo "Health check OK (http://localhost:${APP_PORT}/)"
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

