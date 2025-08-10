//groovy
def remote = [:] // map
def git_url = "git@github.com:AnastasiyaGapochkina01/hapijs.git"

pipeline {
    agent any
    parameters {
        gitParameter (name: 'branch', type: 'PT_BRANCH', quickFilterEnabled: true, defaultValue: 'main')
    }
    environment {
        DOCKER_REPO = "anestesia01/happy-js"
        TOKEN = credentials('docker_token')
        PRJ_NAME = "happy-js-1"
        HOST = "10.129.0.39"
    }
    stages {
        stage('Prepare credentials') {
            steps {
                // Credentials Binding
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: 'private_key', usernameVariable: 'username')]){
                    script {
                        remote.name = "${env.HOST}"
                        remote.host = "${env.HOST}"
                        remote.user = "$username"
                        remote.identity = readFile "$private_key"
                        remote.allowAnyHosts = true
                    }
                }
            }
        }
        stage('Clone repo') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: "${branch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-key', url: "$git_url"]]])
            }
        }
        stage('Build and push') {
            steps {
                script {
                    sh """
                      docker build -t "${env.DOCKER_REPO}:${env.BUILD_ID}" ./
                      docker login -u anestesia01 -p ${env.TOKEN}
                      docker push "${env.DOCKER_REPO}:${env.BUILD_ID}"
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    //ssh Pipeline Steps
                    sshCommand remote: remote, command:
                    """
                      docker pull "${env.DOCKER_REPO}:${env.BUILD_ID}"
                      docker rm ${env.PRJ_NAME} --force 2> /dev/null || true
                      docker run -d -it --name ${env.PRJ_NAME} -p 3000:3000 "${env.DOCKER_REPO}:${env.BUILD_ID}"
                    """
                }
            }
        }
    }
}
