pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    stages {
        stage('clean workspace ') {
            steps {
                cleanWs()
            }
        }//clean workspace 
        stage('clone git repo') {
            steps {
                git 'https://github.com/princewillopah/CI-withJenkins-CD-withJenkins-Amazon-Frontend.git'
            }
        }//clone git repo
         stage("Sonarqube Analysis "){
             steps{
                 withSonarQubeEnv('my-SonarQube-servers') {
                     sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=CI-withJenkins-CD-withJenkins-Amazon-Frontend \
                     -Dsonar.projectKey=CI-withJenkins-CD-withJenkins-Amazon-Frontend '''
                 }
             }
         }
         stage("Quality Gate"){
            steps {
                 script {
                     waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token' 
                 }
             } 
         }
         stage('Install Dependencies') {
             steps {
                 sh "npm install"
             }
         }
         stage('TRIVY FS SCAN') {
             steps {
                 sh "trivy fs . > trivyfs.txt"
             }
         }
          stage("Docker Build & Push"){
             steps{
                 script{
                    withDockerRegistry(credentialsId: 'dockerhub-credentials', toolName: 'docker'){   
                        sh "docker build -t amazon-frontend ."
                        sh "docker tag amazon-frontend princewillopah/amazon-frontend:latest "
                        sh "docker push princewillopah/amazon-frontende:latest "
                     }
                 }
             }
         }
         stage("TRIVY"){
             steps{
                 sh "trivy image princewillopah/amazon-frontend:latest > trivyimage.txt" 
             }
         } 

    }// Stages
}// Pipeline
