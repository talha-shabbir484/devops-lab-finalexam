pipeline {
    agent any

    environment {
        NODEJS_HOME = tool name: 'NodeJS 16', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
        PATH = "${env.NODEJS_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/devops-lab-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Lint') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('Coverage Check') {
            steps {
                sh '''
                    npm run coverage
                    COVERAGE=$(cat coverage/coverage-summary.json | grep -m1 '"pct"' | awk '{print $2}' | tr -d ',')
                    echo "Coverage: $COVERAGE"
                '''
            }
        }

        stage('Archive Artifacts') {
            when {
                expression {
                    return sh(script: "git describe --tags --exact-match || true", returnStdout: true).trim() == "release"
                }
            }
            steps {
                sh 'zip -r build.zip *'
                archiveArtifacts artifacts: 'build.zip', fingerprint: true
            }
        }
    }

    post {
        success {
            mail to: 'your-email@gmail.com',
                 subject: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline completed successfully."
        }
        failure {
            mail to: 'your-email@gmail.com',
                 subject: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline failed. Check Jenkins logs."
        }
    }
}
