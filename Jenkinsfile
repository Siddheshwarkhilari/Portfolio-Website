pipeline {
  agent {
    docker {
      image 'siddhesh111/node:latest'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout') {
      steps {
        checkout([
             $class: 'GitSCM',
             branches: [[name: '*/main']], // Replace 'main' with the correct branch name
             userRemoteConfigs: [[url: 'https://github.com/Siddheshwarkhilari/Portfolio-Website']]
      }
    }
    stage('Install dependencies') {
      steps {
         echo 'Installing Node.js dependencies'
         sh 'npm install'  // Installs dependencies from package.json
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "siddhesh111/portfolio-website:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
