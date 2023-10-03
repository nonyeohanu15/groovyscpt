# groovyscpt

pipeline{
    agent any
    tools {
        maven "maven4"
        jdk "myjdk"
    }
    stages {
        
        stage('Fetch code'){
            
            steps{
                git branch: 'master', url: 'https://github.com/iswatsal/sampleapplication.git'
            }
            
        }
          stage('02-Validate') {
              steps{
                  sh 'mvn validate'
              }
          }
            stage('03-compile'){
                
	        steps{
	           sh 'mvn compile'
	        }
            } 
            
             stage('04-review') {
                 
                 steps{
                     sh 'mvn -P metrics pmd:pmd'
                 }
             }
              stage('05-test'){
                  steps{
                      sh 'mvn test'
                  }
              }
              
              stage('06-code coverage'){
                  steps{
                      sh 'mvn verify '
                  }
              }
              
              stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        
         stage('08-build-artifacts'){
             
             steps{
                 sh 'mvn install -DskipTests'
             }
         
             post {
	           success {
	              echo 'Now Archiving it...'
	              archiveArtifacts artifacts: '**/target/*.war'
	           }
	        }
        }
        
        stage('version-artifacts'){
            steps{
              sh 'mkdir -p nonye'
              sh 'cp target/sampleapp.war nonye/sampleapp-BUILD_DI.war'
            }
        }
    }
    
    
}
