pipeline {
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout') {
      steps {
         git branch: 'main', url: 'https://github.com/gopinekkanti/my-cicd-pipeline-argoCD.git'
      }
    }
    stage('Build and Test') {
      steps {
        // build the project and create a JAR file
        sh 'cd my-cicd-pipeline-argoCD/spring-boot-app && mvn clean package'
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "nekkantigopi/mydockerimages:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "my-cicd-pipeline-argoCD/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'cd my-cicd-pipeline-argoCD/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
  }
}
