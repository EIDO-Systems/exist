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
    node {
        checkout scm
    }
    stages {
        stage('Test') {
            script {
                sh 'mvn -B --no-transfer-progress verify'
            }
        }
        stage('Build') {
            script {
                sh 'mvn -B clean package -DskipTests -Ddependency-check.skip=true -Ddocker=false'
            }
        }
    }
}