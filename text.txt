pipeline {
  agent {
    docker {
      image 'mcr.microsoft.com/playwright:v1.47.1-jammy'
      args '-u root:root'
    }
  }

  tools {
    nodejs "NodeJS_20_LTS"
  }

  options {
    ansiColor('xterm')
    timestamps()
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
        sh 'npx playwright test --reporter=html'
      }
    }

    stage('Publish Report') {
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
    always {
      archiveArtifacts artifacts: 'playwright-report/**/*.*', onlyIfSuccessful: true
      echo "✅ Build complete. Previous report replaced with the latest one."
    }
  }
}
