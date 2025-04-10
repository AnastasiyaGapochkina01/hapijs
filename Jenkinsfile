def remote = [:]
def git_url = "git@github.com:AnastasiyaGapochkina01/hapijs.git"
pipeline {
  agent any
  parameters {
    gitParameter (name: 'branch', type: 'PT_BRANCH', quickFilterEnabled: true)
  }

  environment {
    DOCKER_REPO = "anestesia01/demo"
    TOKEN = credentials('hub_token')
    CONT_NAME = "hapyjs-1"
    HOST = "10.129.0.25"
  }

  stages {
    stage('Clone repo') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: "${branch}"]], doGenerateSubmoduleConfigurations: false, extentions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-ssh-key', url: "$git_url"]]])
      }
    }

  stage('Prepare credentials') {
    steps {
      withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-ssh-key', keyFileVariable: 'private_key', usernameVariable: 'username')]) {
        script {
          remote.name = "${env.HOST}"
          remote.host = "${env.HOST}"
          remote.user = "$username"
          remote.identity = readFile("$private_key")
          remote.allowAnyHosts = true
        }
      }
    }
  }

   stage('Build and push') {
     steps {
       withCredentials([usernamePassword(credentialsId: 'hub_token', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
           script {
             sh """
               sudo docker build -t "${env.DOCKER_REPO}:${env.BUILD_ID}" ./
               sudo docker login -u ${USERNAME} -p ${PASSWORD}
               sudo docker push "${env.DOCKER_REPO}:${env.BUILD_ID}"
             """
           }
       }
    }
   }
  stage('Deploy') {
    step {
        script {
          sshCommand remote: remote, command: """
             set -ex ; set -o pipefail
             sudo docker pull "${env.DOCKER_REPO}:${env.BUILD_ID}"
             sudo docker rm ${env.CONT_NAME} --force 2> /dev/null || true
             sudo docker run -d -it --name ${env.CONT_NAME} ${env.DOCKER_REPO}:${env.BUILD_ID}
          """
        }
      }
    }
 }
}
