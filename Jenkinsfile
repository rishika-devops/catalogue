pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }
    environment {
        packageversion = ''
    }
    options {
        timeout(time: 1 , unit : 'HOURS')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    stages {
        stage('get version') { 
            steps {
                script {
                    def PackageJson = readJSON file: 'package.json'
                    packageversion = PackageJson.version
                    echo "application version: $packageversion"
                } 
            }
        }
        stage('install dependancies') { 
            steps {
                sh """
                  npm install
                """  
            }
        }
        stage('build') { 
            steps {
                sh """
                  ls -la
                  zip -q -r catalogue.zip ./* -x ".git" -x "*.zip"
                  ls -ltr
                """  
            }
        }
    }
    post {
        always {
            echo " i will always say hello again"
            deleteDir()
        }
        failure {
            echo " this runs when pipeline is failed , used generally to send some alerts"
        }
        success {
            echo " i will say hello when pipeline is success"
        }
    }
}


