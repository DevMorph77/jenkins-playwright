pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        cleanWs()
        git branch: 'main', url: 'https://github.com/DevMorph77/jenkins-playwright.git'
      }
    }

    stage('Install Playwright & Dependencies') {
      steps {
        bat """
          echo 📦 Installing dependencies...
          call npm ci || call npm install
          call npx playwright install
        """
      }
    }

    stage('Run Playwright Tests') {
      steps {
        bat """
          echo 🧪 Running Playwright tests and generating HTML report...
          call npx playwright test --reporter=html
        """
      }
    }

    stage('Publish Playwright Report') {
      steps {
        script {
          sleep(time: 3, unit: 'SECONDS') // ✅ wait briefly
          bat 'dir playwright-report'     // ✅ confirm directory
          publishHTML([
            reportDir: 'playwright-report',
            reportFiles: 'index.html',
            reportName: 'Playwright Test Report',
            alwaysLinkToLastBuild: true,
            keepAll: true,
            includes: '**/*'
          ])
        }
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'playwright-report/**/*.*', fingerprint: true
      echo "✅ Build successful — Playwright report archived and published."
    }
    failure {
      echo "❌ Tests failed — check console output for details."
    }
  }
}
