pipeline {

    agent any

    environment {
        ANSIBLE_PLAYBOOK = 'apply.yml'   // The playbook that runs the role
    }

    stages {
        stage('Checkout SCM') {
            steps {
            checkout scm
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                // Run the Ansible playbook using the Ansible plugin
                ansiblePlaybook(
                    playbook: "${ANSIBLE_PLAYBOOK}",
                    colorized: true
                )
            }
        }
        stage('Deploy') {
            steps {
            sh 'echo "This is my Deploy step"'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/results/*'
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}