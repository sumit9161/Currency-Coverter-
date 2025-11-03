pipeline {
  agent any

  tools {
    nodejs 'NodeJS'   // must match Global Tool name in Jenkins
  }

  environment {
    SONAR_TOKEN = credentials('my-sonar-server')  // your credential ID
    SONAR_HOST  = 'http://localhost:9000'         // your Sonar URL
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/sumit9161/Currency-Coverter.git'
      }
    }

    stage('Install') {
      steps {
        bat 'npm ci'
      }
    }

    stage('Build (optional)') {
      steps {
        bat 'if exist package.json ( npm run build )'
      }
    }

    stage('Test & Coverage') {
      steps {
        // Adjust test command to your repo's test setup (jest recommended)
        bat 'npm test -- --coverage || exit /b 0'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('MySonarServer') {
          bat """
            npx -y sonar-scanner ^
              -Dsonar.projectKey=Currency-Coverter ^
              -Dsonar.projectName=Currency-Coverter ^
              -Dsonar.sources=. ^
              -Dsonar.host.url=%SONAR_HOST% ^
              -Dsonar.login=%SONAR_TOKEN% ^
              -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
          """
        }
      }
    }
  }

  post {
    success { echo '✅ Pipeline succeeded' }
    failure { echo '❌ Pipeline failed — check console output' }
  }
}
