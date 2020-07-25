
pipeline {
      options {
        buildDiscarder(logRotator(daysToKeepStr: '10'))
        timestamps()
    }
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
        
                stage('testing / unit') {
            agent {
                label 'master'
            }

          steps {
                      
              echo "*******************************************************************************"
              sh 'echo "before changing image in Deploy"'
              echo "><><><><><> Pretent Unit test ><><><><><>><><><"
              sh 'cat application.yaml'
              echo "*******************************************************************************"            
            
          }
            post {
            always {
                     sh 'echo "Updating variables  completed"'
                   }
           }                           
            
          }

    }
}
