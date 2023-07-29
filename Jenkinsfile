pipeline {
      agent
	{
  		node
  		{
  			label 'docker-slave'
  		}
	}

    triggers {
      pollSCM ''
    }
     environment
    { 

        PROJECT = 'sample-springboot-app'
        IMAGE = 'sample-springboot-app:latest'
        DOCKERREPO= 'bmaddi' 
        ARTIURL = 'artifactory-new-93bf8f5a14c88dd6.elb.eu-west-2.amazonaws.com:8082'
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker-hub-creds')
        DOCKER_REGISTRY_URL = 'https://registry.hub.docker.com'


    }
    stages {
    stage('Build preparations')
        {
            steps
            {
                script
                {
		    // calculate GIT lastest commit short-hash
                    gitCommitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    shortCommitHash = gitCommitHash.take(7)
                    // calculate a sample version tag
                    VERSION = shortCommitHash
                    currentBuild.displayName = "${PROJECT}-${VERSION}"
                    IMAGE = "$DOCKERREPO/$PROJECT:${BUILD_NUMBER}.${VERSION}"
                }
            }
        }
            stage('Docker build')
        {
            steps
            {
                script
                {
                    // Build the docker image using a Dockerfile
                    docker.build("$IMAGE","-f Dockerfile .")
                }
            }
        }

               stage('Docker push')
        {
            steps
            {
                script
                {
                     withDockerRegistry(credentialsId: DOCKER_REGISTRY_CREDENTIALS, url: DOCKER_REGISTRY_URL) {
                      sh "docker push ${IMAGE}"
                    }
                }

                }
            }
    }
                    
    post {
      success {
       echo 'job is success'
      }
      aborted {
        echo 'aborted'
      }
      failure {
        echo 'failed job'
      }
      cleanup {
        cleanWs()
      }
  }
}
