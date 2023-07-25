pipeline{  
  environment {
    registry = "kaan123/nodejs-helloworld"
    registryCredential = 'gizemtekiner-dockerhub'
    dockerImage = ''
    SONARSERVER = 'sonarserver'
    SONARSCANNER = 'sonarscanner'
  }
  agent any
    stages {
        stage('Build'){
           steps{
              script{
                sh 'npm install'
              } 
           }   
        }
              stage('sonar analysis'){
                environment{
                  scannerHome = tool "${SONARSCANNER}"  
                }
            
           steps{
                withSonarQubeEnv("${SONARSERVER}") {
                  sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                  -Dsonar.projectName=vprofile \
                  -Dsonar.projectVersion=1.0 \
                  -Dsonar.sources=src/ \
                  -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                  -Dsonar.junit.reportsPath=target/surefire-reports/ \
                  -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                  -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
           }   
                                  }
        stage('Building image') {
            steps{
                script {
                  dockerImage = docker.build registry + ":latest"
                 }
             }
          }
          stage('Push Image') {
              steps{
                  script {
                       docker.withRegistry( '', registryCredential){                            
                       dockerImage.push()
                      }
                   }
                } 
           }
           stage('Deploying into k8s'){
            steps{
              script {
                  sh "cat deployment.yaml"
                  sh "kubectl --kubeconfig=/home/mildevops/.kube/config get pods"
                  sh "kubectl --kubeconfig=/home/mildevops/.kube/config apply -f deployment.yaml"
              }
          }
        }
    }
}
