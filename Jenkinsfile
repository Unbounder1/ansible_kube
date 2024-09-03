pipeline {

    agent {
        kubernetes {
            label 'ansible-agent'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: ansible-ubuntu
                image: unbounder1/ansible-ubuntu:latest
                command:
                - cat
                tty: true
            """
        }
    }

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
                playbook: 'path/to/playbook.yml',
                inventory: 'path/to/inventory.ini',
                credentialsId: 'sample-ssh-key',
                colorized: true)
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