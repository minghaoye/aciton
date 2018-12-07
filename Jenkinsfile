pipeline {
  agent {
    node {
      label 'nodejs'
    }

  }
  stages {
    stage('SCM') {
      steps {
        git(url: 'https://github.com/kubesphere/devops-docs-sample.git', changelog: true, poll: false)
      }
    }
    stage('get dependencies') {
      steps {
        container('nodejs') {
          sh 'yarn'
        }

      }
    }
    stage('unit test') {
      steps {
        container('nodejs') {
          sh 'yarn test'
        }

      }
    }
    stage('build') {
      steps {
        container('nodejs') {
          sh 'yarn build'
        }

      }
    }
    stage('build & push snapshot imgae') {
      steps {
        container('nodejs') {
          sh 'docker build -t docker.io/$DOCKERHUB_ORG/$APP_NAME:SNAPSHOT-$BUILD_NUMBER .'
          withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : 'dockerhub-id' ,)]) {
            sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
            sh 'docker push docker.io/$DOCKERHUB_ORG/$APP_NAME:SNAPSHOT-$BUILD_NUMBER'
          }

        }

      }
    }
    stage('deploy to dev') {
      steps {
        input(id: 'deploy', message: 'deploy to dev?', submitter: 'pengfei')
        kubernetesDeploy(configs: 'deploy/no-branch-dev/**', enableConfigSubstitution: true, kubeconfigId: 'demo-kubeconfig')
      }
    }
  }
}
