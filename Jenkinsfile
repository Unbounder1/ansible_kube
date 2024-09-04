pipeline {

    agent {
        kubernetes {
            label 'ansible-agent'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: ansible
                image: debian:latest
                command:
                - cat
                tty: true
                securityContext:
                  runAsUser: 0
            """
        }
    }

    environment {
        ANSIBLE_PLAYBOOK = 'apply.yml' 
    }

    stages {
        stage('Install Ansible') {
            steps {
                container('ansible') {
                    sh '''
                        apt-get update && \
                        apt-get install -y ansible python3 python3-pip
                    '''
                }
            }
        }
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                container('ansible') {
                    sh '''
                        ansible-playbook ./apply.yml --ssh-extra-args='-o StrictHostKeyChecking=no'
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                container('ansible'){
                    sh 'kubectl version'
                }
                
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