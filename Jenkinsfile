pipeline {
    agent any

    environment {
        IMAGE_NAME = "terence92140/timesheet"
        IMAGE_TAG  = "1.1"
        NAMESPACE  = "chap4"
        DEPLOYMENT = "timesheet-dep"
        CONTAINER  = "timesheet"
    }

    stages {

        stage('GIT') {
            steps {
                checkout scm
            }
        }

        stage('COMPILATION') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('INSTALLATION') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }

                sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('DEPLOIEMENT') {
            steps {
                sh 'kubectl -n ${NAMESPACE} set image deployment/${DEPLOYMENT} ${CONTAINER}=${IMAGE_NAME}:${IMAGE_TAG}'
                sh 'kubectl -n ${NAMESPACE} rollout status deployment/${DEPLOYMENT}'
            }
        }
    }
}
