pipeline {

  environment {
    podmanImageName = "joedayz/react-app"
    podmanImageTag = "latest"
    PATH = "/usr/local/bin:$PATH"  
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
          withCredentials([usernamePassword(credentialsId: "${registryCredential}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh """
              podman login -u ${USERNAME} -p ${PASSWORD} registry.hub.docker.com
              podman push ${podmanImageName}:${podmanImageTag} registry.hub.docker.com/${podmanImageName}:${podmanImageTag}
            """
          }
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