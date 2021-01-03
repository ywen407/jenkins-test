pipeline {
    agent:any

    stages {
        stage('Prepare') {
            agent any

            steps {
                echo ' Cloning master Repository'

                git url: 'https://github.com/ywen407/jenkins-test',
                    branch:'main',
                    credentialsId: 'git-credential'
            }
        }

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

        stage('Test Backend') {
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
                npm run test
                '''
            }
          }
        }

        /*stage('Bulid Backend') {
          agent any
          steps {
            echo 'Build Backend'

            dir ('./'){
                sh """
                docker build . -t server --build-arg env=${PROD}
                """
            }
          }

          post {
            failure {
              error 'This pipeline stops here...'
            }
          }
          */
        }


    }
}