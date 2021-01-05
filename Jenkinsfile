def buildBadge = addEmbeddedableBadgeConfiguration(id:"signalbuild", subject: "Signal BUild")

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
                script {
                  buildBadge.setStatus("running")
                  buildBadge.setColor("grey")
                }

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
            post {

                failure {
                  script {
                      buildBadge.setStatus('lint fail')
                      buildBadge.setColor('red')
                  }
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
            failure {
              script {
                buildBadge.setStatus('test fail')
                buildBadge.setColor('red')
              }
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
                script {
                  buildBadge.setStatus('build success')
                  buildBadge.setColor('green')
                }
              }
              failure {
                script {
                  buildBadge.setStatus('build fail')
                  buildBadge.setColor('red')
                }
              }
          }
        }

    }
}