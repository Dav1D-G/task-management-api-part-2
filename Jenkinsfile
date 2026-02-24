pipeline {
  agent none

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  parameters {
    choice(name: 'ENV', choices: ['dev', 'test'], description: 'Target environment')
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run automated tests')
    string(name: 'BUILD_VERSION', defaultValue: '0.1.0', description: 'Build version label')
  }

  stages {
    stage('Checkout') {
      agent { label 'ci' }
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      agent { label 'ci' }
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      agent { label 'ci' }
      when {
        expression { return params.RUN_TESTS }
      }
      steps {
        sh 'npm test'
      }
    }

    stage('Build Metadata') {
      agent { label 'ci' }
      steps {
        script {
          def content = "BUILD_VERSION=${params.BUILD_VERSION}\nENV=${params.ENV}\nRUN_TESTS=${params.RUN_TESTS}\n"
          writeFile file: 'build-info.txt', text: content
          echo "Build info written: BUILD_VERSION=${params.BUILD_VERSION}, ENV=${params.ENV}, RUN_TESTS=${params.RUN_TESTS}"
        }
        archiveArtifacts artifacts: 'build-info.txt', allowEmptyArchive: false
      }
    }

    stage('Credentials Check') {
      agent { label 'docker' }
      steps {
        withCredentials([string(credentialsId: 'demo-token', variable: 'DEMO_TOKEN')]) {
          sh 'test -n "$DEMO_TOKEN"'
        }
        echo 'Credential loaded successfully'
      }
    }

    stage('Docker Build') {
      agent { label 'docker' }
      steps {
        sh 'docker build -t task-api:local .'
      }
    }

    stage('Docker Run') {
      agent { label 'docker' }
      steps {
        sh '''
          set -e
          docker rm -f task-api-local >/dev/null 2>&1 || true
          docker run -d --name task-api-local -p 3000:3000 task-api:local
          for i in $(seq 1 10); do
            if curl -fsS http://host.docker.internal:3000/health >/dev/null; then
              echo "Health check OK"
              exit 0
            fi
            sleep 1
          done
          echo "Health check failed"
          exit 1
        '''
      }
    }
  }

  post {
    success {
      echo 'Build completed successfully'
    }
    failure {
      echo 'Build failed ? check logs'
    }
    always {
      node('docker') {
        sh 'docker rm -f task-api-local >/dev/null 2>&1 || true'
      }
      echo 'Post-build actions completed'
    }
  }
}
