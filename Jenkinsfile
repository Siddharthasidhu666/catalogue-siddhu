pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
         appVersion = ''
         REGION = "us-east-1"
         ACC_ID = "590183868932"
         PROJECT = "roboshop"
         COMPONENT = "catalogue"


    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    parameters {
        booleanParam(name: 'deploy', description: 'Toggle this value')
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
        stage('Docker Build') {
            steps{
                script{
                    withAWS(credentials: 'aws-cred', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }
                }
            }
        }
        stage('Trigger Deploy') {
            when{
                expression { params.deploy }
            }
            steps {
                script {
                    build job: 'catalogue-cd',
                    parameters: [
                        string(name: 'appVersion', value: "${appVersion}"),
                        string(name: 'deploy_to', value: 'dev')
                    ],
                    propagate: false,  // even SG fails VPC will not be effected
                    wait: false // VPC will not wait for SG pipeline completion
                }
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'Hello Success'
        }
        failure { 
            echo 'Hello Failure'
        }

    }
}






