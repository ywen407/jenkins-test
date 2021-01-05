void setBuildStatus(String message, String state) {
  step([
    $class: "GitHubCommitStatusSetter",
    reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/ywen407/jenkins-test"],
    contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
    errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
    // 아래 값을 Display URL for Blue Ocean 플러그인 설치 후 활성화 한다.
    // statusBackrefSource: [$class: "ManuallyEnteredBackrefSource", backref: "${env.RUN_DISPLAY_URL}"],
    statusResultSource: [
      $class: "ConditionalStatusResultSource",
      results: [
        [$class: "AnyBuildResult", message: message, state: state]]
      ]
  ]);
}

pipeline {
    agent any

    stages {
        stage('Ready') {
            agent any

            steps {
                echo ' Cloning master Repository'

                git url: 'https://github.com/ywen407/jenkins-test',
                    branch:'main',
                    credentialsId: 'git-credential'
            }
        }
        /*
        stage('Sonarqube') {
            environment {
                scannerHome = tool 'SonarQubeScanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        */
        stage('Lint Backend') {
            // Docker plugin and Docker Pipeline 두개를 깔아야 사용가능!
            agent {
              docker {
                image 'node:latest'
              }
            }

            steps {
              dir ('./'){
                  sh '''
                  npm install &&
                  npm run lint
                  '''
              }
            }
        }

        stage('Test and Coverage') {
          agent {
            docker {
              image 'node:latest'
            }
          }
          steps {
            echo 'Test Backend'

            dir ('./'){
                sh '''
                npm install &&
                npm run test &&
                npm run test:e2e &&
                npm run test:cov
                '''
            }
          }

          post {
            success {
              setBuildStatus("Build succeeded", "SUCCESS");
            }
            failure {
              setBuildStatus("Build failed", "FAILURE");
            }
          }
        }

        stage('Bulid Backend') {
          agent {
            docker {
              image 'node:latest'
            }
          }
          steps {
            echo 'Build Backend'

            dir ('./'){
                sh """
                npm run build
                """
            }
          }

          post {
              success {
                setBuildStatus("Build succeeded", "SUCCESS");
              }
              failure {
                setBuildStatus("Build failed", "FAILURE");
              }
          }
        }

    }
}