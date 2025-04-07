def git_url = "git@github.com:AnastasiyaGapochkina01/hapijs.git"
pipeline {
  agent any
  parameters {
    gitParameter (name: 'branch', type: 'PT_BRANCH', quickFilterEnabled: true)
  }

  environment {
    DOCKER_REPO = "anestesia01/demo"
    TOKEN = credentials('hub_token')
  }

  stages {
    stage('Clone repo') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: "${branch}"]], doGenerateSubmoduleConfigurations: false, extentions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-ssh-key', url: "$git_url"]]])
      }
    }

   stage('Build and push') {
     steps {
       withCredentials([usernamePassword(credentialsId: 'hub_token', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
           script {
             sh """
               sudo docker build -t "${env.DOCKER_REPO}:${env.BUILD_ID} ./"
               sudo docker login -u ${USERNAME} -p ${PASSWORD}
               sudo docker push "${env.DOCKER_REPO}:${env.BUILD_ID}"
             """
           }
       }
    }
   }
  }
}
  
