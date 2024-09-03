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
                image: jenkins/inbound-agent:alpine
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
                        apk add --no-cache ansible
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
                        ansible-playbook -i path/to/inventory.ini path/to/playbook.yml --ssh-extra-args='-o StrictHostKeyChecking=no'
                    '''
                }
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