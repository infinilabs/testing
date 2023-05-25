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
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-es710.yml && exit 1'
                }
              }
              post {
                failure {
                  sh script: '/home/jenkins/bin/notify-feishu.py "[Testing Gateway against Elasticsearch 7.10.2] tests failed" "" "$BUILD_URL"'
                }
              }
            }
            stage('Testing Gateway against Elasticsearch 8.6.x') {
              agent {
                label 'linux'
              }
              steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-es86.yml'
                }
              }
              post {
                failure {
                  sh script: '/home/jenkins/bin/notify-feishu.py "[Testing Gateway against Elasticsearch 8.6.x] tests failed" "" "$BUILD_URL"'
                }
              }
            }
            stage('Testing Gateway against EasySearch 1.0') {
              agent {
                label 'linux'
              }
              steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
                  sh label: 'testing-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-uat-easysearch.yml'
                }
              }
              post {
                failure {
                  sh script: '/home/jenkins/bin/notify-feishu.py "[Testing Gateway against EasySearch 1.0] tests failed" "" "$BUILD_URL"'
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
              sh label: 'testing-loadgen', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/loadgen-uat.yml'
            }
          }
          post {
            failure {
              sh script: '/home/jenkins/bin/notify-feishu.py "[Testing Loadgen] tests failed" "" "$BUILD_URL"'
            }
          }
        }
        stage('Benchmarking Gateway') {
          agent {
            label 'linux'
          }
          steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
              sh label: 'benchmark-gateway', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/gateway-benchmark.yml'
            }
          }
          post {
            failure {
              sh script: '/home/jenkins/bin/notify-feishu.py "[Benchmarking Gateway] tests failed" "" "$BUILD_URL"'
            }
          }
        }
        stage('Testing EasySearch') {
          agent {
            label 'linux'
          }
          steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
              sh label: 'testing-easysearch', script: 'cd /home/jenkins/go/src/infini.sh/testing && ./bin/loadrun -config ./suites/jenkins/easysearch-uat.yml'
            }
          }
          post {
            failure {
              sh script: '/home/jenkins/bin/notify-feishu.py "[Testing EasySearch] tests failed" "" "$BUILD_URL"'
            }
          }
        }
      }
    }
  }
  post {
    failure {
      sh script: '/home/jenkins/bin/notify-feishu.py "[testing] jenkins build failed" "" "$BUILD_URL"'
    }
  }
}
