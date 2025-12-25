pipeline {
  agent any
   

  environment {
    APP_DIR = 'node/plain/webappWithTests/Application'
  }

  stages {
    stage('Install') {
      steps {
        dir("${APP_DIR}") {
          sh '''
            node -v
            npm -v
            npm ci
          '''
        }
      }
    }

    stage('Package') {
      steps {
        sh '''
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
