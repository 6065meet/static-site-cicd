pipeline {
  agent any

  environment {
    IMAGE_NAME = "6065meet/static-site"
    KUBE_NAMESPACE = "portfolio"
  }

  stages {
    stage('Clone Repository') {
      steps {
        git 'https://github.com/6065meet/static-site-cicd.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          docker.build("${IMAGE_NAME}:latest")
        }
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh """
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push ${IMAGE_NAME}:latest
          """
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh """
          kubectl delete deployment static-site -n ${KUBE_NAMESPACE} --ignore-not-found
          kubectl apply -f k8s/deployment.yaml -n ${KUBE_NAMESPACE}
          kubectl apply -f k8s/service.yaml -n ${KUBE_NAMESPACE}
        """
      }
    }
  }
}
