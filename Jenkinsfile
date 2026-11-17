pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Set up') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . .venv/bin/activate
                    python -m pytest --junitxml=reports/junit.xml
                '''
            }
        }

        stage('Package') {
            steps {
                sh 'mkdir -p dist && tar czf dist/app-${BUILD_NUMBER}.tar.gz app'
            }
        }
    }

    post {
        always {
            junit testResults: 'reports/junit.xml', allowEmptyResults: true
        }
        success {
            archiveArtifacts artifacts: 'dist/*.tar.gz'
            echo "Build ${BUILD_NUMBER} passed."
        }
        failure {
            echo "Build ${BUILD_NUMBER} failed. Check the console output and test results."
        }
    }
}
