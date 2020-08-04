pipeline {
    // agent {
    //     docker {
    //         image 'node:6-alpine'
    //         args '-p 3000:3000 -p 5000:5000'
    //     }
    // }
    agent any
    environment {
        CI = 'true'
        env.NODEJS_HOME = "${tool 'node'}"
        env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"
    }
    stages {
 
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver for development') {
            when {
                branch 'dev'
            }
            steps {
                sh './jenkins/scripts/deliver-for-development.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
        stage('Deploy for production') {
            when {
                branch 'prod'
            }
            environment {
                SSH_CREDS = credentials('cbf9acb1-da9a-4409-9812-3bede055c082')
                PORT = '9122'
                HOST = 'athenslabs.gr'
            }
            steps {
                sh "echo ${SSH_CREDS}"
                sh "ssh -o StrictHostKeyChecking=no -i ${SSH_CREDS} -p ${PORT} ${SSH_CREDS_USR}@${HOST} pwd"
                sh './jenkins/scripts/deploy-for-production.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}