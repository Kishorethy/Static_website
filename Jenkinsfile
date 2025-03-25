pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'kishorethy/static_website'
        DOCKER_TAG = 'latest'
        DOCKER_CREDENTIALS_ID = '230eb724-54fb-49c6-8046-1321ed84dc9d'
        GIT_REPO = 'https://github.com/Kishorethy/Static_website.git'
        GIT_BRANCH = 'main'
        SONARQUBE_SERVER = 'MySonarQube'  // SonarQube server name from Jenkins configuration
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube scan
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh '/opt/sonarscanner/sonar-scanner-6.2.1.4610-linux-x64/bin/sonar-scanner -Dsonar.projectKey=Static_website -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_5eebaede6912e32b144b52f78226b3ec6aeb3653'
                    }
                }
            }
        }

        stage('Testing') {
            steps {
                script {
                    sh 'docker run -d ${DOCKER_IMAGE}'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        sh 'docker push ${DOCKER_IMAGE}:${DOCKER_TAG}'
                    }
                }
            }
        }
    }
}
