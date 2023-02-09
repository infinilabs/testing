pipeline {

    agent none

    environment {
        CI = 'true'
    }
    stages {
        stage('Running test suites') {

            agent {
                label 'linux'
            }

            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                   sh 'cd /home/jenkins/go/src/infini.sh/loadgen && git fetch && git reset --hard origin/master'
                   sh 'cd /home/jenkins/go/src/infini.sh/gateway && git fetch && git reset --hard origin/master'
                   sh 'cd /home/jenkins/go/src/infini.sh/testing && git fetch && git reset --hard origin/main'

                   sh label: 'build-load-runner', script: 'cd /home/jenkins/go/src/infini.sh/loadgen/cmd/load-runner && make build && cp ./bin/load-runner /home/jenkins/go/src/infini.sh/testing/bin/'
                   sh label: 'build-gateway', script: 'cd /home/jenkins/go/src/infini.sh/gateway && make build && cp ./bin/gateway /home/jenkins/go/src/infini.sh/testing/bin/'
                   sh label: 'build-loadgen', script: 'cd /home/jenkins/go/src/infini.sh/loadgen && make build && cp ./bin/loadgen /home/jenkins/go/src/infini.sh/testing/bin/'
                   sh label: 'test', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/load-runner -config ./suites/jenkins.yml'
                }
            }
        }

    }
}
