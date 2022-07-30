pipeline {
    agent any
    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '3'))
        ansiColor('xterm')
    }
    environment {
        JAVA_HOME = '/usr/lib/jvm/java-8-openjdk-amd64'
    }
    post {
        always {
            cleanWs()
        }
    }
    stages {
        stage('Build') {
            steps {
                sh """
                    sed -i -e 's/build.debug = on/build.debug = off/g' build.properties
                    ./build.sh clean clean-all all dist-bz2 dist-zip
                """
            }
        }
        stage('Deploy') {
            when {
                allOf {
                    expression { currentBuild.result == 'SUCCESS' }
                    expression { env.BRANCH_NAME == 'develop-4.x.x' }
                }
            }
            steps {
                sh """
                    test -d s3-uploads || mkdir s3-uploads
                    find dist -maxdepth 1 -iname "eXist*" -type f | while read -r item; do
                        case \$item in
                            *.zip) mv "\$item" s3-uploads/latest-4.x.x-win.zip
                                ;;
                            *.tar.bz2) mv "\$item" s3-uploads/latest-4.x.x-nix.tar.bz2
                                ;;
                        esac
                    done
                    aws s3 sync s3-uploads s3://eido-exist-builds/
                """
            }
        }
    }
}