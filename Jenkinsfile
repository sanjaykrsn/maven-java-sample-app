pipeline {
    agent any
    tools {
        jdk 'java' 
        maven 'maven'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Jan-stan/maven-java-sample-app.git'
            }
        }
        stage('compile') {
            steps {
                bat 'mvn spring-javaformat:apply'
                bat 'mvn clean compile'
            }
        }
        stage('built') {
            steps {
                bat 'mvn package'
            }
        }
        stage('push docker image') {
            steps {
               script {
                    // Docker image details
                    def dockerImage = 'janstan94/maven-java-sample-app'
                    def dockerTag = 'latest'

                    // Docker login
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    }

                    // Build the Docker image
                    bat "docker build -t ${dockerImage}:${dockerTag} ."

                    // Push the Docker image to Docker Hub
                    bat "docker push ${dockerImage}:${dockerTag}"
                } 
            }
        }
        stage('deploy application') {
            steps {
                script {
                    // Pull and run the Docker container
                    def dockerImage = 'janstan94/maven-java-sample-app'
                    def dockerTag = 'latest'
                    bat "docker pull ${dockerImage}:${dockerTag}"
                    bat "docker run -d -p 8001:8001 --name maven-app ${dockerImage}:${dockerTag}"
                }
            }
        }
    }
}
