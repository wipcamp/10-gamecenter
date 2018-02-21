pipeline {
  agent any
  environment {
    GIT_BRANCH = "${BRANCH_NAME}"
  }
  stages {
    stage('build-image') {
      steps {
        sh 'sudo docker build . -t 10-gamecenter'
        sh 'sudo docker tag 10-gamecenter registry.wip.camp/10-gamecenter:$GIT_BRANCH'
        sh 'sudo docker tag 10-gamecenter registry.wip.camp/10-gamecenter'
      }
    }
    stage('push-image') {
      steps {
        sh 'sudo docker push registry.wip.camp/10-gamecenter:$GIT_BRANCH'
        sh 'sudo docker push registry.wip.camp/10-gamecenter'
      }
    }
    stage('versioning') {
      when {
        expression {
          return GIT_BRANCH == 'master'
        }
      }
      steps {
        sh 'sudo docker tag 10-gamecenter registry.wip.camp/10-gamecenter:$GIT_BRANCH-$BUILD_NUMBER'
        sh 'sudo docker push registry.wip.camp/10-gamecenter:$GIT_BRANCH-$BUILD_NUMBER'        
        sh 'sudo docker image rm registry.wip.camp/10-gamecenter:$GIT_BRANCH-$BUILD_NUMBER'        
      }
    }
    stage('clean') {
      steps {
        sh 'sudo docker image rm registry.wip.camp/10-gamecenter:$GIT_BRANCH'
        sh 'sudo docker image rm registry.wip.camp/10-gamecenter'
        sh 'sudo docker image rm 10-gamecenter'
      }
    }
    stage('deploy-development') {
      when {
        expression {
          return GIT_BRANCH == 'develop'
        }
      }
      steps {
        sh 'sudo kubectl rolling-update 10-gamecenter -n development --image registry.wip.camp/10-gamecenter:$GIT_BRANCH --image-pull-policy Always'
      }
    }
  }
  post {
    success {
      sh 'echo success'
    }
    failure {
      sh 'echo failure'
    }
  }
}