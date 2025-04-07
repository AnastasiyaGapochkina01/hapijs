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
       script {
         def Image = docker.build("${env.DOCKER_REPO}:${env.BUILD_ID}")
         docker.withRegistry('https://registry-1.docker.io', '${TOKEN}') {
           Image.push()
         }
       }
     }
    }
  }
}
  
