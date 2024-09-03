pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from SCM (e.g., Git)
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Compile or build your project if necessary
                sh 'echo "Building the project..."'
            }
        }

        stage('Test') {
            steps {
                // Run your tests
                sh 'echo "Running tests..."'
                // Example: If you have a shell script for testing
                sh './run-tests.sh'
            }
        }

        stage('Deploy') {
            steps {
                // Deployment steps (optional)
                sh 'echo "Deploying application..."'
            }
        }
    }

    post {
        always {
            // Archive the test results or any important logs
            archiveArtifacts artifacts: '**/test-results/*'
        }

        success {
            echo 'All tests passed!'
        }

        failure {
            echo 'Some tests failed.'
        }
    }
}