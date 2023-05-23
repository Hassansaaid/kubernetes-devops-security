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
        withDockerRegistry([credentialsId: "DOCKER-HUB",url:""]){
        sh "printenv"
        sh 'docker build . -t hassan/devsecops:""$GIT_COMMIT""'
        sh 'docker push hassan/devsecops:""$GIT_COMMIT""'
      }
      }
    }
    }
  
}
