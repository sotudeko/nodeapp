pipeline {
  agent {
    docker {
      // Docker image with node and git installed
      image 'tarampampam/node:13-alpine'
    }
  }

  environment {
    HOME = '.'
  }

  stages {

    stage('Build') {
      steps {
        sh 'npm install'
      }
    }

    stage('Test') {
      steps {
        echo "Run acceptance tests to ensure that IQ remediation changes function as expected in the application"
      }
    }

    stage('Policy Evaluation') {
      // Policy evaluation should only take place against the branch we intend to merge to
      when { branch 'master' }
      steps {
        sh 'npm run build'  // build script using webpack and the copy-modules-webpack-plugin for easy scanning
        nexusPolicyEvaluation iqStage: 'build', iqApplication: 'npm-example',
            iqScanPatterns: [[scanPattern: 'webpack-modules']],
            failBuildOnNetworkError: true
      }
    }
  }
}   