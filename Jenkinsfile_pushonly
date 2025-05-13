def IMAGE_VERSION
pipeline {
   agent any
      stages {
	 stage("Checkout") {
            steps {
               checkout scm
            }
         }
         stage('Docker Build') {
            steps {
               script {
                  echo "${env.IMAGE_REPO}"
                  app = docker.build("${env.IMAGE_REPO}")
               }
            }
         }
         stage('Push Image') {
            steps {
                script {
		    IMAGE_VERSION = sh(script: "head -n 1 Dockerfile | sed 's/#//'", returnStdout: true).trim()
                    docker.withRegistry("https://registry.hub.docker.com/${env.IMAGE_REPO}", "dockerhub-cred") {            
                        app.push(IMAGE_VERSION)
                        app.push("latest")
                    }
		    echo "${env.IMAGE_REPO}:${IMAGE_VERSION} pushed"
                }
            }
         }
    }
}
