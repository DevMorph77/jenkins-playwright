pipeline {
  agent any   // ✅ run directly on Jenkins host

  tools {
    nodejs "NodeJS_20_LTS"   // make sure this is configured under Manage Jenkins → Tools
  }

  options {
    timestamps()
    ansiColor('xterm')
  }

  stages {
    stage('Checkout') {
      steps {
        cleanWs()
        git branch: 'main', url: 'https://github.com/DevMorph77/jenkins-playwright.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Run Playwright Tests') {
      steps {
        sh 'npx playwright install --with-deps'
        sh 'npx playwright test --reporter=html'
      }
    }

    stage('Publish Report') {
      steps {
        script {
          publishHTML(target: [
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: false,  // ✅ replace old report each build
            reportDir: 'playwright-report',
            reportFiles: 'index.html',
            reportName: 'Playwright Test Report'
          ])
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'playwright-report/**/*.*', onlyIfSuccessful: true
      echo "✅ Build complete. New Playwright report published."
    }
  }
}
