pipeline {
    agent {
        docker {
            image 'siddhesh111/node:v1'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Replace 'main' with the correct branch name
                    userRemoteConfigs: [[url: 'https://github.com/Siddheshwarkhilari/Portfolio-Website']]
                ])
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
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    sh 'apt install -y docker.io && systemctl start docker'
                    echo "Building Docker image: ${DOCKER_IMAGE}"
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                    
                    echo "Pushing Docker image: ${DOCKER_IMAGE}"
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        def dockerImage = docker.image("${DOCKER_IMAGE}")
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
