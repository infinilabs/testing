pipeline {

  agent none

  environment {
    CI = 'true'
  }
  stages {
    stage('Preparing binaries') {

      agent {
        label 'linux'
      }

      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
          sh 'cd /home/jenkins/go/src/infini.sh/loadgen && git fetch && git reset --hard origin/master'
          sh 'cd /home/jenkins/go/src/infini.sh/gateway && git fetch && git reset --hard origin/master'
          sh 'cd /home/jenkins/go/src/infini.sh/testing && git fetch && git reset --hard origin/main && git clean -dfx'

          sh label: 'build-loadrun', script: 'cd /home/jenkins/go/src/infini.sh/loadgen/cmd/loadrun && make build && cp ./bin/loadrun /home/jenkins/go/src/infini.sh/testing/bin/'
          sh label: 'build-gateway', script: 'cd /home/jenkins/go/src/infini.sh/gateway && make build && cp ./bin/gateway /home/jenkins/go/src/infini.sh/testing/bin/'
          sh label: 'build-loadgen', script: 'cd /home/jenkins/go/src/infini.sh/loadgen && make build && cp ./bin/loadgen /home/jenkins/go/src/infini.sh/testing/bin/'
        }
      }
    }
    stage('Running Testing Suites') {
      parallel {
        stage('Testing Gateway') {
          stages {
            stage('Testing Gateway against Elasticsearch 7.10.2') {
              agent {
                label 'linux'
              }
              steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-es710.yml || /home/jenkins/bin/notify-feishu.py "[Testing Gateway against Elasticsearch 7.10.2] failed"'
                }
              }
            }
            stage('Testing Gateway against Elasticsearch 8.6.x') {
              agent {
                label 'linux'
              }
              steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-es86.yml || /home/jenkins/bin/notify-feishu.py "[Testing Gateway against Elasticsearch 8.6.x] failed" '
                }
              }
            }
            stage('Testing Gateway against EasySearch 1.0') {
              agent {
                label 'linux'
              }
              steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-easysearch.yml || /home/jenkins/bin/notify-feishu.py "[Testing Gateway against EasySearch 1.0] failed"'
                }
              }
            }
          }
        }
        stage('Testing Loadgen') {
          agent {
            label 'linux'
          }
          steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
              sh label: 'testing-loadgen', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/loadgen-uat.yml || /home/jenkins/bin/notify-feishu.py "[Testing Loadgen] failed"'
            }
          }
        }
        stage('Benchmarking Gateway') {
          agent {
            label 'linux'
          }
          steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
              sh label: 'benchmark-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-benchmark.yml || /home/jenkins/bin/notify-feishu.py "[Benchmarking Gateway] failed"'
            }
          }
        }
        stage('Testing EasySearch') {
          agent {
            label 'linux'
          }
          steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
              sh label: 'testing-easysearch', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/easysearch-uat.yml || /home/jenkins/bin/notify-feishu.py "[Testing EasySearch] failed"'
            }
          }
        }
      }
    }
  }
}
