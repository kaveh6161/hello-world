pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'app-builder.yaml'
    }
  }
  stages {
    stage('Checkout') {
        steps {
            // Checkout the code from the GitHub repository
            git 'https://github.com/kaveh6161/hello-world.git'
        }
    }
    stage('Build') {
        steps {
            // Build the project with Maven
            sh 'mvn clean install'
        }
    }
  }
  
  /*
  post {
    failure {
        // If the pipeline fails, send an email
        mail to: 'kaveh.saleki@gmail.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
    }
  }
  */ 
}