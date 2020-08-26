pipeline {
  agent {
    node {
      label 'master'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh 'yarn install'
        sh 'yarn build'
      }
    }

    stage('Test') {
      post {
        success {
          publishHTML([
                                    allowMissing: false,
                                    alwaysLinkToLastBuild: false,
                                    keepAll: true,
                                    reportDir: 'output/coverage/jest',
                                    reportFiles: 'index.html',
                                    reportName: 'Test Report'
                                ])
          }

        }
        steps {
          sh 'pwd'
          sh 'cd build'
          sh 'ls -la'
          sh 'http-server -p 9000 . > /dev/null 2>&1 &'
          sh 'sleep 5'
          sh 'curl -I http://localhost:9000/index.html'
          sh 'yarn test --detectOpenHandles'
          sh 'echo done!!!'
        }
      }

      stage('Package') {
        post {
          success {
            archiveArtifacts(artifacts: "${RELEASE_NAME}", fingerprint: true)
          }

        }
        steps {
          sh 'pwd'
          sh 'ls -la'
          sh "tar -czvf ${RELEASE_NAME} -C build/ ."
        }
      }

    }
    environment {
      RELEASE_NAME = "release.${BUILD_ID}.tar.gz"
    }
    post {
      always {
        echo 'Build completed!!'
      }

    }
    options {
      buildDiscarder(logRotator(numToKeepStr: '10'))
    }
  }