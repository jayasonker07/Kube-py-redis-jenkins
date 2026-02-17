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
 
    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl apply -f .
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