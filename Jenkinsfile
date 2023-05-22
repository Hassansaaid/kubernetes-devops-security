pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' // hassan changes 
            }
        }   
    stage('Unit Tests') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      } 
     }
    stage('build docker image'){
      steps{
        sh "sudo docker build . -t hassan/devsecops"
      }
    }
    }
  
}
