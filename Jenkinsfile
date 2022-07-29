pipeline {
    agent any
    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        ansiColor('xterm')
    }
    post {
        unsuccessful {
            cleanWs()
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -q clean package -DskipTests -Ddependency-check.skip=true -Ddocker=false'
            }
        }
    }
}