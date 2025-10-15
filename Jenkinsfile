pipeline {
  agent any   // ✅ runs on Jenkins host (Windows), no Docker

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
          publishHTML(target: [
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: false,   // ✅ replace old report each time
            reportDir: 'playwright-report',
            reportFiles: 'index.html',
            reportName: 'Playwright Test Report'
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
