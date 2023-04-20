pipeline {
    environment {
        registry = "leandro2m/demo-app"
        registryCredential = "leandro2m"
    }
    agent  any

    stages {
        stage('SAST') {
            steps {
                script {
                    sh '''env | grep -E "JENKINS_HOME|BUILD_ID|GIT_BRANCH|GIT_COMMIT" > /tmp/env
                        docker pull registry.fortidevsec.forticloud.com/fdevsec_sast:latest
                        docker run --rm --env-file /tmp/env --mount type=bind,source=$PWD,target=/scan registry.fortidevsec.forticloud.com/fdevsec_sast:latest'''
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    img = registry + ":${env.BUILD_ID}"
                    println ("${img}")
                    println ("${registryCredential}")
                    dockerImage = docker.build("${img}")
                }
            }
        }
        stage ("Push to DockerHub") {
            steps {
                script {
                    docker.withRegistry("https://registry.hub.docker.com", registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage ("FortiCNP Image Scan") {
            steps {
                 fortiCWPScanner imageName: "${img}", block: true
            }
        }
        stage('Deploy stage enviroment') {
            steps {
                script {
                    sh 'docker pull leandro2m/aws-cli:latest'
                    sh "docker run leandro2m/aws-cli kubectl set image deployment/demo-app demo-app=leandro2m/demo-app:${env.BUILD_ID}"
                }
            }
        }
          stage('FortiDevSec DAST') {
            steps {
                script {
                    sh '''env | grep -E "JENKINS_HOME|BUILD_ID|GIT_BRANCH|GIT_COMMIT" > /tmp/env
                            docker pull registry.fortidevsec.forticloud.com/fdevsec_dast:latest
                            docker run --rm --env-file /tmp/env --mount type=bind,source=$PWD,target=/scan registry.fortidevsec.forticloud.com/fdevsec_dast:latest'''
                }
            }
        }
        stage('Deploy in production') {
            steps {
                script {
                    sh 'docker pull leandro2m/aws-cli:latest'
                    sh "docker run leandro2m/aws-cli kubectl set image deployment/demo-app demo-app=leandro2m/demo-app:${env.BUILD_ID}"
                }
            }
        }
        
    }
}
