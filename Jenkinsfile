pipeline {
      agent: any
    triggers {
      pollSCM ''
    }
     environment
    { 

        PROJECT = 'sample-springboot-app'
        IMAGE = 'sample-springboot-app:latest'
        DOCKERREPO= 'bmaddi' 

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
                     withDockerRegistry([ credentialsId: "docker-hub-creds", url: "" ]) {
                     docker.image(IMAGE).push()
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
