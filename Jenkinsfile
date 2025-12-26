pipeline {
  agent any

  environment {
    APP_DIR   = "node/plain/webappWithTests/Application"
    PROD_DIR  = "/opt/prod-server"
    APP_PORT  = "8092"

    IMAGE_REPO = "tesco4/webappwithtests"
    IMAGE_TAG  = "build-${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
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
        echo 'No tests configured – skipping'
      }
    }

    stage('Docker Build') {
      steps {
        dir("${APP_DIR}") {
          sh 'docker build -t ${IMAGE_REPO}:${IMAGE_TAG} .'
        }
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            set -e
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push ${IMAGE_REPO}:${IMAGE_TAG}
          '''
        }
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          set -e
          mkdir -p /opt/prod-server
          rm -rf /opt/prod-server/*
          cp -r node/plain/webappWithTests/Application/* /opt/prod-server/
        '''
      }
    }

    stage('Run') {
      steps {
        sh '''
          set -e
          pkill -f "node server.js" || true
          cd /opt/prod-server
          nohup env PORT=8092 node server.js > app.log 2>&1 &
          sleep 2
          tail -n 20 app.log || true
        '''
      }
    }

    stage('Health Check') {
      steps {
        sh 'curl -sSf http://localhost:8092/ >/dev/null'
      }
    }
  }
}
