pipeline {
  agent {label 'Jaya-Kube'}
  triggers {
    pollSCM('H/2 * * * *')
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Check the docker cli') {
      steps {
        sh "docker --version"
        sh "docker ps -a"
      }
    }
    stage('Build Docker Image') {
      steps {
        sh '''
          docker build -t jayasonker/py-feb26:$BUILD_NUMBER python-sample-code
        '''
      }
    }

    stage('Push Docker Image') {
        steps {
            withCredentials([
            usernamePassword(
                credentialsId: 'Jaya-dockerhub-creds',
                usernameVariable: 'DOCKERHUB_USER',
                passwordVariable: 'DOCKERHUB_PASS'
            )
            ]) {
            sh '''
                echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                docker push jayasonker/py-feb26:$BUILD_NUMBER
                docker logout
            '''
            }
        }
    }

    stage('Deploy - Template and Apply') {
        steps {
            sh '''
            export IMAGE_TAG=$BUILD_NUMBER
            envsubst < py-deploy.yaml | kubectl apply -f -
            kubectl rollout status deployment/py-deploy
            '''
        }
    }

    stage('Check on Kubernetes') {
      steps {
        sh '''

          kubectl get pods
          kubectl get svc
          kubectl get deployments
        '''
      }
    }

    stage('Application Health Check') {
      steps {
        sh '''
          sleep 10
          curl http://192.168.49.2:30110
        '''
      }
    }
  }
}