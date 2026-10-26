pipeline {
  agent any
  environment {
    DOCKERHUB_USER = 'manel131'
    BACKEND_IMAGE  = "${DOCKERHUB_USER}/gestion-projets-backend"
    FRONTEND_IMAGE = "${DOCKERHUB_USER}/gestion-projets-frontend"
    API_URL        = 'http://192.168.33.10:8085'
    TAG            = "${BUILD_NUMBER}"
  }
  stages {
    stage('Checkout app (prof)') {
      steps {
        dir('DevOps-AppGestionDesProjets') {
          git url: 'https://github.com/Alaa-Rami/DevOps-AppGestionDesProjets.git', branch: 'main'
        }
      }
    }
    stage('Build images') {
      steps {
        sh '''
          docker build -f backend.Dockerfile -t $BACKEND_IMAGE:$TAG -t $BACKEND_IMAGE:latest DevOps-AppGestionDesProjets
          docker build -f frontend.Dockerfile --build-arg API_URL=$API_URL -t $FRONTEND_IMAGE:$TAG -t $FRONTEND_IMAGE:latest DevOps-AppGestionDesProjets
        '''
      }
    }
    stage('Push images') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                         usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push $BACKEND_IMAGE:$TAG
            docker push $BACKEND_IMAGE:latest
            docker push $FRONTEND_IMAGE:$TAG
            docker push $FRONTEND_IMAGE:latest
          '''
        }
      }
    }
  }
  post {
    always { sh 'docker logout || true' }
  }
}
