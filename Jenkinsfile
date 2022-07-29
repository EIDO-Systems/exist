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
        stage('Test') {
            steps {
                sh 'mvn -B --no-transfer-progress verify'
            }
            post {
                always {
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B clean package -DskipTests -Ddependency-check.skip=true -Ddocker=false'
            }
        }
    }
}