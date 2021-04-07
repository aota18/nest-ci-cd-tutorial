pipeline {
    agent any

    // Cron Syntax
    // Drive this pipelin every 3 minutes
    triggers {
        pollSCM('*/3 * * * *')
    }


    // Credentials file
     environment {
        AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
        AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        HOME = '.' // Avoid npm root owned
    }

    stages{

    
    stage('Prepare'){
        agent any

        steps {
            echo "Let's start Long Journey"

            echo "Let's start Long Journey ! 🙌 "
                echo "Clonning Repository..."

                git url: "https://github.com/aota18/nest-ci-cd-tutorial.git",
                    branch: 'master',
                    credentialsId: 'git-test'
        }


        post {
            success {
                echo 'Successfully Cloned Repository'
            }

            always {
                echo "I tried..."
            }

            cleanup {
                echo " after all other post condition"
            }
        }
    }


    stage('Build Backend'){
        agent {
            docker {
                image 'node:latest'
            }
        }

        steps {
            echo 'Build Backend'

            sh 'npm install'
            sh 'npm run build'
        }

        post {
            failure {
                error "This pipeline stops here..."
            }
        }
    }

    stage('Build Docker'){
        agent any

        steps {
            dir('./'){
                sh 'docker build . -t backend'
            }
        }
    }

    stage('Deploy Backend'){
        agent any 

        steps {
            echo 'Deploy Backend'

            sh 'docker ps -f name=backend -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=frontend -q | xargs -r docker container rm'
            sh 'docker images --notrunc --all --quiet --filter="dangling=true" | xargs --no-run-if-empty docker rmi'
            sh 'docker run -p 3000:3000 -d --name backend backend'
        }

        post {
            success {
                mail    to: "sapphire031794@gmail.com",
                        subject: "[Backend] Deploy Success",
                        body: 'Backend Successfully deployed!'
            }
        }
    }

    }
}