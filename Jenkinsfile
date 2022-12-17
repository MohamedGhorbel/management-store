pipeline {
    agent any
    environment{
    DOCKER_CREDS=credentials('DOCKER_CREDS')
    }
    stages {
        stage('build') {
            steps {
                bat 'mvn package'
            }
        }
        stage('build docker image') {
            steps {
               bat "docker build -t sm:${env.BUILD_ID} ."
            }
        }
        stage('push to registry'){
                                             steps{
                                                 bat " docker login --username ${DOCKER_CREDS_USR} --password ${DOCKER_CREDS_PSW}"
                                                 bat "docker tag sm:${env.BUILD_ID} ${DOCKER_CREDS_USR}/sm:${env.BUILD_ID}"
                                                 bat "docker push ${DOCKER_CREDS_USR}/sm:${env.BUILD_ID}"
                                             }

                                         }

        stage('deploy') {
                    steps {

                bat 'docker-compose -p store-man up -d '
                retry(3) {
               timeout(20) {
                            bat "curl http://localhost:8088/store-management/api/medications/hello?username=farouk"
                                      }
                                    }
                                    }
                                }
                            }

    post {
       always {
            junit 'target/*reports/*.xml'
            emailext attachLog: true, body: 'test success', subject: 'test notification', to: 'hablous2@gmail.com'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
            powershell 'docker-compose -p store-management up -d'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }

}
