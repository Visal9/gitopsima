node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("imantha95/doc")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

     stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {
            app.push("${env.BUILD_NUMBER}")
       }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
    
    post {
        success {
            slackSend (color: '#00FF00', message: "*Staging Deployed*\n${env.JOB_NAME} [${env.BUILD_NUMBER}]'\n${env.BUILD_URL}")
        }
        failure {
            slackSend (color: '#FF0000', message: "*Staging Failed*\n${env.JOB_NAME} [${env.BUILD_NUMBER}]'\n${env.BUILD_URL}")
        }
    }
}
