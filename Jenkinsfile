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
                image: registry.access.redhat.com/ubi8/ubi:latest
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
        TEST_ENVIRONMENTS = 'debian,redhat'
    }


    stages {
        stage ('Init'){
            parallel{
                stage('Checkout SCM') {
                    steps {
                        checkout scm
                    }
                }
                stage ('Debian Dependencies'){
                    steps {
                        container('debian') {
                            sh '''
                                apt-get update && \
                                apt-get install -y ansible python3 python3-pip
                            '''
                        }
                    }
                }
                stage ('RHEL Dependencies'){
                    steps {
                        container('redhat') {
                            sh '''
                                dnf update -y && \
                                dnf install -y python39 && \
                                pip3 install ansible 
                            '''
                        }
                    }
                }           
            }
        }
            
        stage ('Apply the ansible playbook'){
            steps {
                script{
                    def containers = TEST_ENVIRONMENTS.split(',')
                    def cmds = { containerName -> container(containerName) {
                        sh '''
                            ansible-playbook ./apply.yml --ssh-extra-args='-o StrictHostKeyChecking=no'
                        '''
                    }
                    }
                    def parallelStages = containers.collectEntries{
                        ["${it}", { cmds(it)}]
                    }
                    parallel parallelStages
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    def containers = TEST_ENVIRONMENTS.split(',')
                    containers.each { 
                        containerName -> container(containerName) {
                            sh 'kubectl version'
                        }
                    }
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