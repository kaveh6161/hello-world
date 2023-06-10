pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }
  stages {
    stage('Git Checkout') {
        steps {
            // Checkout the code from the GitHub repository
            git 'https://github.com/kaveh6161/hello-world.git'
        }
    }

    stage('App Build') {
        steps {
            container('maven') {
                // Build the project with Maven
                script{
                    sh 'mvn clean install'
                    sh 'pwd'
                    sh 'ls -l'
                    // sh 'cp target/*.war /home/jenkins/shared'
                }
            }
        }
        post {
            success {
                // Archive the Artifacts
                archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
            }
        }
    }

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          // List the contents of the current directory
          sh 'ls -l /home/jenkins/shared'
          // Build the docker image with tag based on the Jenkins build job number
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=kaveh61/regapp:${BUILD_NUMBER}
            '''
          }
        }
      }
    }

    stage('Deploy App to Kubernetes') {     
      steps {
        container('kubectl') {
          // Deploy the web app to my Home LAB k8s cluster
          withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" regappDeploy.yml'
            sh 'kubectl apply -f regappDeploy.yml'
          }
        }
      }
    }
  }

  post {
    failure {
        // If the pipeline fails, send an email
        mail to: 'kaveh.saleki@gmail.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
    }
  }
}