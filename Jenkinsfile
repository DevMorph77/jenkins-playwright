pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        cleanWs()
        echo "🧹 Workspace cleaned. Checking out latest code..."
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
          echo "🧾 Checking if Playwright report folder exists..."
          def reportPath = "playwright-report"
          // Check if folder exists before publishing
          if (fileExists(reportPath)) {
            echo "✅ Report folder found. Publishing HTML report..."
            bat "dir ${reportPath}"

            publishHTML([
              reportDir: reportPath,
              reportFiles: 'index.html',
              reportName: 'Playwright Test Report',
              alwaysLinkToLastBuild: true,
              keepAll: true,
              includes: '**/*'
            ])
          } else {
            echo "⚠️ No Playwright report found — skipping HTML publish."
          }
        }
      }
    }
  }

  post {
    success {
      echo "✅ Build successful — archiving Playwright report..."
      archiveArtifacts artifacts: 'playwright-report/**/*.*', fingerprint: true
      echo "📊 Report archived and published successfully."
    }
    failure {
      echo "❌ Tests failed — check console output and ensure the report is generated."
      script {
        if (fileExists('playwright-report')) {
          echo "📁 Archiving failed test report for inspection..."
          archiveArtifacts artifacts: 'playwright-report/**/*.*', fingerprint: true
        } else {
          echo "⚠️ No report folder found after failure — nothing to archive."
        }
      }
    }
    always {
      echo "🏁 Pipeline finished. Check Jenkins console and HTML reports for details."
    }
  }
}
