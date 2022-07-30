pipeline {
    agent any
    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        ansiColor('xterm')
    }
    post {
        always {
            cleanWs()
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -q clean package -DskipTests -Ddependency-check.skip=true -Ddocker=false'
            }
        }
        stage('Deploy') {
            when {
                allOf {
                    expression { currentBuild.result == 'SUCCESS' }
                    expression { env.BRANCH_NAME == 'develop' }
                }
                steps {
                    sh """
                        test -d s3-uploads || mkdir s3-uploads
                        find exist-distribution/target/ -maxdepth 1 -iname "exist-distribution*" -type f | while read -r item; do
                            case \$item in
                                *win.zip) mv "\$item" s3-uploads/latest-win.zip
                                    ;;
                                *unix.tar.bz2) mv "\$item" s3-uploads/latest-nix.tar.bz2
                                    ;;
                            esac
                        done
                        aws s3 sync s3-uploads s3://eido-exist-builds/"""
                    }
                }
            }
        }
    }
}