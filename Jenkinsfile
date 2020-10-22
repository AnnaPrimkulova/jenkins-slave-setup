pipeline {
    agent {
        docker {
            image 'ansible/ansible:default'
            label 'slave-builder'
            args '-u 0:0 -v /home/jenkins/.ssh:/root.ssh'
        } //docker
    } //agent
    stages {
        stage("Set up") {
        steps {
            sh """
                pip install ansible
                apt-get install zip -y
            """
        } // steps
    } //stage
    stage("Build") {
        steps {
            sh """
                ansible-playbook -i inventory jenkins-slave-setup.yml
            """
        } //steps 
    } //stage
    stage("Test") {
        steps {
            sh """
                ansible jenkins-slaves -m shell -a "ls /home/jenkins_slave"
                ansible jenkins-slaves -m shell -a "id jenkins"
                ansible jenkins-slaves -m shell -a "cat /home/jenkins/.ssh.authorized_keys" - inventory
            """
        } //steps
    } //stage
    stage("Deploy") {
        steps {
            sh """
            zip "jenkins-slave-setup.zip" .
            scp jenkins-slave-setup.zip root@192.168.5.133:/var/www/html/my-repository
        """
        } //steps
    } //stages
}