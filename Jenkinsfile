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
                sh 'ls -al'
                sh "sed -i 's#20152282/${APP_NAME}:.*#20152282/${APP_NAME}:${COMMIT_ID}#' manifests/deployment.yaml"
                echo "Image updated succcessfully"
                sh "git config --global user.name 'nalajala9' && git config --global user.email 'nalajalaravi99@gmail.com'"
                sh "git add manifests/deployment.yaml && git commit -m 'update deployment to use latest image'"
                withCredentials([gitUsernamePassword(credentialsId:'github-credentials',usernameVariable:'GITHUB_USERNAME',passwordVariable:'GITHUB_PASSWORD')]){
                    sh "git pull && git push origin master"
                }
                echo "Pushed Sucessfully"
            }
        }
    }
}




// stage('Checkout') {
//             steps {
//                 git branch: 'main', url: 'https://github.com/nalajala9/python-flask.git'
//             }
//         }
