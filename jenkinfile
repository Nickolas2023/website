pipeline {

    agent any

    environment {
        IMAGE_NAME = "abode-webapp"
    }

    stages {

        stage('Job1 - Build') {

            steps {

                checkout scm

                sh '''
                    echo "Building ${BRANCH_NAME}"
                    docker build \
                      -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                '''
            }
        }


        stage('Job2 - Test') {

            steps {

                sh '''
                    docker rm -f abode-test || true

                    docker run -d \
                      --name abode-test \
                      -p 8080:80 \
                      ${IMAGE_NAME}:${BUILD_NUMBER}

                    sleep 5

                    docker rm -f abode-test
                '''
            }
        }


        stage('Job3 - Prod') {

            when {
                branch 'master'
            }

            steps {

                echo "MASTER detected - deploying to production"

                sh '''
                    docker rm -f abode-prod || true

                    docker run -d \
                      --name abode-prod \
                      --restart always \
                      -p 80:80 \
                      ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {

        always {
            sh 'docker rm -f abode-test || true'
        }

    }
}
