pipeline {
    agent any
    stages {
        stage('Build Application') {
            steps {
                sh 'mvn -f pom.xml clean package'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts........"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Create Tomcat Docker Image'){
            steps {
                sh "pwd"
                sh "ls -a"
                //sh "docker build . -t tomcatsamplewebapp:${env.BUILD_ID}"
                sh 'docker build -t userservice:$BUILD_NUMBER .'
            }
                  post {
                always {
                    sh 'echo "Docker build  completed"'
                }
           }
        }
        
                stage('Updating variables') {
            agent {
                label 'master'
            }

          steps {
            dir('k8s/scripts') {
              
              echo "*******************************************************************************"
              sh 'echo "before changing image in Deploy"'
              sh 'cat application.yaml'
              echo "*******************************************************************************"
            }
            
          }
            post {
            always {
                     sh 'echo "Updating variables  completed"'
                   }
           }                           
            
          }

    }
}
