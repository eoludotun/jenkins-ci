
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
      
        stage('Deploy to kubernetes cluster') {
            agent {
                label 'master'
            }
             environment {
                SECRET = credentials('my-fake-password')
               // AUTOSCALE = credentials('prod-autoscaling')
            }
          steps {

            //dir('k8s/scripts') {       
               //sh "sed -i 's|eoludotun/userservice:00|eoludotun/userservice:$BUILD_NUMBER|' application.yaml
              
              sh "sed -i 's|POSTGRES_PASSWORD|${SECRET}|' application.yaml"
              //sh "sed -i 's|3|${AUTOSCALE}|' application.yaml"
              sh "sed -i 's|eoludotun/userservice:00|userservice:$BUILD_NUMBER|' application.yaml"
             
              withCredentials([kubeconfigFile(credentialsId: 'poc-eks-kubernetes', variable: 'KUBECONFIG')]) {
                
                echo "*******************************************************************************"
                sh 'cat application.yaml'
                sh 'echo "After changing image in Deploy"'
                sh "kubectl --kubeconfig $KUBECONFIG apply -f ./application.yaml --record"
                echo "*******************************************************************************"
                echo "===========================Verify if deployment is successful =================="
                //sh "kubectl --kubeconfig $KUBECONFIG rollout status deployment user-deployment -n ${params.NameSpace}"
                //sh "kubectl --kubeconfig $KUBECONFIG get deployments -n ${params.NameSpace} -o wide"
                echo "*******************************************************************************"
                  
           // }

          }
           }
      }               
                    
                    
     }

   }
}
