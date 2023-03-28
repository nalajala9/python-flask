pipeline {
    agent any

    environment {
        APP_NAME = 'flask-jenkins'
        COMMIT_ID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
    }

    stages {
        stage('BuildImage') {
            steps {
                sh 'docker build -t 20152282/${APP_NAME}:${COMMIT_ID} .'
            }
        }
        stage('PushImage') {
            steps {
                withCredentials([usernamePassword(credentialsId:'docker-credentials',usernameVariable:'DOCKER_USERNAME',passwordVariable:'DOCKER_PASSWORD')]){
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }
                sh 'docker push 20152282/${APP_NAME}:${COMMIT_ID}'
            }
        }
        stage('Update Deployment') {
            steps {
                script {
                    def manifestRepo = 'https://github.com/nalajala9/python-flask.git'
                    def deploymentFilePath = 'deployment.yaml'
                    sh 'ls -al'
                    sh "sed 's#20152282/${APP_NAME}:.*#20152282/${APP_NAME}:${COMMIT_ID}#' manifests/${deploymentFilePath}"

                    echo "Commit ID: ${COMMIT_ID}"
                    echo "New Docker Image: 20152282/${APP_NAME}:${COMMIT_ID}"
                    echo "Deployment manifest updated in ${manifestRepo}/manifests/${deploymentFilePath}"
                }
            }
        }
    }
}




// stage('Checkout') {
//             steps {
//                 git branch: 'main', url: 'https://github.com/nalajala9/python-flask.git'
//             }
//         }
