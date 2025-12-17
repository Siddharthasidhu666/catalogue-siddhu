pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
         appVersion = ''
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    stages {
        stage('Read package.json') {
            steps {
                script {

                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "Package version: ${appVersion}"

                }
            }
        }
        stage('install dependencies'){
            steps{
                script{
                    sh '''
                    npm install
                    '''
                }
                
            }
        }
    }
}