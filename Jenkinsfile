pipeline {
    environment {
        imagename = "navyaani14/pubrepo"
        dockerImage = ''
        containerName = 'my-container'
        dockerHubCredentials = 'jenkins'
    }

    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'your-bitbucket-credentials-id', url: 'https://bitbucket.org/ananthkannan/myawesomeangularapprepo/']]])
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn -f pom.xml clean install'
                }
            }
        }

        stage('Building Image') {
            steps {
                script {
                    dockerImage = docker.build("${imagename}:${BUILD_NUMBER}", "MyAwesomeApp")
                }
            }
        }

        stage('Running Image') {
            steps {
                script {
                    sh "docker run --rm -d --name ${containerName} ${dockerImage.imageName()}"
                }
            }
        }

        stage('Stop and Remove Container') {
            steps {
                script {
                    sh "docker stop ${containerName} || true"
                    sh "docker rm ${containerName} || true"
                }
            }
        }

        stage('Deploy Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: dockerHubCredentials, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                        sh "docker push ${dockerImage.imageName()}"
                    }
                }
            }
        }
    }
}
