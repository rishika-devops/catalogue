pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }
    environment {
        packageversion = ''
        nexusURL = '3.86.47.135:8081'
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
        stage('unit testing') { 
            steps {
                sh """
                  echo "unit test cases will run here"
                """  
            }
        }
        stage('sonar scan') { 
            steps {
                sh """
                  sonar-scanner
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
        stage('publish artifacts'){
            steps{
                nexusArtifactUploader(
                   nexusVersion: 'nexus3',
                   protocol: 'http',
                   nexusUrl: "${nexusURL}",
                   groupId: 'com.roboshop',
                   version: "${packageversion}",
                   repository: 'catalogue',
                   credentialsId: 'nexus-auth',
                   artifacts: [
                      [artifactId: 'catalogue',
                       classifier: '',
                       file: 'catalogue.zip',
                       type: 'jar']
        ]
     )
            }
        }
        stage('deploy'){
            steps{
                script{
                    def params = [
                        string(name:'version', value: "$packageversion"),
                        string(name:'environment', value: "dev")
                    ]
                    build job: "catalogue-deploy" , wait: true , parameters: params
                }
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


