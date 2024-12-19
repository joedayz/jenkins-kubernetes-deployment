pipeline {

  environment {
    podmanImageName = "joedayz/react-app"
    podmanImageTag = "latest"
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/joedayz/jenkins-kubernetes-deployment.git'
      }
    }

    stage('Build Image with Podman') {
      steps {
        script {
          sh """
            podman build -t ${podmanImageName}:${podmanImageTag} .
          """
        }
      }
    }

    stage('Pushing Image with Podman') {
      environment {
        registryCredential = 'dockerhub-credentials'
      }
      steps {
        script {
          sh """
            podman login -u \$(cat /path/to/username-file) -p \$(cat /path/to/password-file) registry.hub.docker.com
            podman push ${podmanImageName}:${podmanImageTag}
          """
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
        }
      }
    }

  }

}