pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 20, unit: 'SECONDS')
        disableConcurrentBuilds()
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
                sh 'sleep 200'
            }
        }
    }
}