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
		  echo 'success : docker build - ${env.IMAGE_REPO}'
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
	            echo 'success : push image ${env.IMAGE_REPO}:${IMAGE_VERSION}'
                }
            }
         }
	 stage('update image tag in k8s manifest') {
            steps {
               script {
		   withCredentials([gitUsernamePassword(credentialsId: 'github-cred')]) {
                      sh "rm -rf *"
	              sh "rm -rf .git"
	              sh "git clone https://github.com/stephenwon/k8s-infra-push ."
	
	              sh "git config user.email jenkins@example.com"
	              sh "git config user.name jenkins"
	              sh """sed -i 's|nodeapp-git-push:.*|nodeapp-git-push:${IMAGE_VERSION}|' clusters/mycluster/nodeapp-deploy.yaml"""
		      sh """sed -i 's|#timestamp:.*|#timestamp:${BUILD_TIMESTAMP}|' clusters/mycluster/nodeapp-deploy.yaml"""
	              sh "cat clusters/mycluster/nodeapp-deploy.yaml"
	  
	              sh "git commit -am 'Update image tag' && git push https://github.com/stephenwon/k8s-infra-push"
	              echo "Pushed image ${IMAGE_VERSION}"
		   }
	       }
	    }
	 }
    }
}
