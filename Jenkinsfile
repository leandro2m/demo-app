pipeline {
    environment {
        registry = "leandro2m/demo-app"
        registryCredential = "leandro2m"
    }
    agent  any

    stages {
        // stage('SAST') {
        //     steps {
        //         script {
        //             sh '''env | grep -E "JENKINS_HOME|BUILD_ID|GIT_BRANCH|GIT_COMMIT" > /tmp/env
        //                 docker pull registry.fortidevsec.forticloud.com/fdevsec_sast:latest
        //                 docker run --rm --env-file /tmp/env --mount type=bind,source=$PWD,target=/scan registry.fortidevsec.forticloud.com/fdevsec_sast:latest'''
        //         }
        //     }
        // }
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
        stage('Deploy'){
            steps {
                sh '''kubectl get pods'''
                }
                
                 /*
                 //If you are sure this deployment is already running and want to change the container image version, then you can use:
                 sh 'kubectl set image deployments/dvwa 371571523880.dkr.ecr.us-east-2.amazonaws.com/dvwaxperts:${BUILD_NUMBER}'*/
            }
        } 

    }
}