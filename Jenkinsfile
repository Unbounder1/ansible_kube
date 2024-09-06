pipeline {

    agent { 
        kubernetes {
            label 'kube-agent'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              volumes:
                - name: checkout-volume
                  emptyDir: {}
              containers:
              - name: redhat
                image: redhat:latest
                command:
                - cat
                tty: true
                securityContext:
                  runAsUser: 0
                volumeMounts:
                - name: checkout-volume
                mountPath: /workspace
              - name: debian
                image: debian:latest
                command:
                - cat
                tty: true
                securityContext:
                  runAsUser: 0
                volumeMounts:
                - name: checkout-volume
                mountPath: /workspace
            """
        }
    }

    environment {
        ANSIBLE_PLAYBOOK = 'apply.yml' 
    }


    stages {
        parallel{
            stage('Checkout SCM') {
                    steps {
                        checkout scm
                    }
                }

            stage('Installing Dependencies') {
                parallel{
                    steps {
                        container('debian') {
                            sh '''
                                apt-get update && \
                                apt-get install -y ansible python3 python3-pip
                            '''
                        }
                    }
                    steps {
                        container('redhat') {
                            sh ```
                                dnf update && \
                                dnf install -y ansible python3 python3-pip
                            ```
                        }
                    }
                }
    

                
            }
            stage(''
            )
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
        // always {
        //     archiveArtifacts artifacts: '**/results/*'
        // }

        success {
            echo 'Deployment successful!'
            emailext body: 'Successfully Deployed',
                subject: 'Deployment Successful',
                to: 'ryadon12@gmail.com'


        }

        failure {
            echo 'Deployment failed!'
        }
    }
}