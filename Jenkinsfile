pipeline{

    agent any 
    
    tools {
        maven 'maven'
    }

    stages{

        stage('Git Checkout'){
           
            steps{

                script{
                 
                 git branch: 'main', url: 'https://github.com/rameshkumarvermagithub/Counter_application.git'

                }
            }
        }
        stage('Unit Test'){

             steps{

              script{
                   
                   sh 'mvn test'

                }
             }
        }
        stage('Integration Test'){

             steps{

              script{
                   
                   sh 'mvn verify -DskipUnitTests'

                }
             }
        }
        stage('Static Code Analysis'){

             steps{

              script{
                   
                  withSonarQubeEnv(credentialsId: 'sonar') {
                     
                     sh 'mvn clean package sonar:sonar'
                  }
                }
             }
        }
        stage('Quality Gate status Check'){

             steps{

              script{
                   
                   waitForQualityGate abortPipeline: false, credentialsId: 'sonar'

                }
             }
        }
        stage('Maven Build'){

             steps{

              script{
                   
                   sh 'mvn clean install'

                }
             }
        }
        // stage('Docker image Building'){

        //      steps{

        //       script{
                   
        //           sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        //           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
        //           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:latest'

        //         }
        //      }
        // }
        // stage('Docker image push'){

        //      steps{

        //       script{
        //           withCredentials([string(credentialsId: 'docker', variable: 'docker')]) {
                     
        //              sh 'docker login -u rameshkumarverma -p ${docker}'
        //              sh 'docker image push rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
        //              sh 'docker image push rameshkumarverma/$JOB_NAME:latest'
        //           }
        //         }
        //      }
        // }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        
                        sh "docker build -t rameshkumarverma/counter-app ."
                        // sh "docker tag  elart rameshkumarverma/ekart:latest"
                        sh "docker push rameshkumarverma/counter-app:latest"
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image rameshkumarverma/counter-app:latest > trivyimage.txt"
            }
        }
        // stage("deploy_docker"){
        //     steps{
        //         sh "docker run -d --name ekart -p 8070:8070 rameshkumarverma/ekart:latest"
        //     }
        // }

        stage('Deploy to kubernets'){
            steps{
                script{
                    // dir('K8S') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deployment-service.yml'
                                // sh 'kubectl apply -f service.yml'
                        }
                    // }
                }
            }
        }

    }
}
