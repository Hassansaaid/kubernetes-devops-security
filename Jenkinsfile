pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "ls -a"
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
    stage("mutation test"){
      steps{
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post{
        always{
          pitmutation mutationStatsFile:'**/target/pit-reports/**/mutations.xml'
        }
      }
    }
    stage('build docker image'){
      steps{
        withDockerRegistry([credentialsId: "DOCKER-HUB",url:""]){
        sh "printenv"
        sh 'docker build . -t hassansaaid/devsecops:""$GIT_COMMIT""'
        sh 'docker push hassansaaid/devsecops:""$GIT_COMMIT""'
      }
      }
    }
    stage("DEPLOY ON K8S"){
      steps{
        withKubeConfig([credentialsId: "KUBECONFIG-1"]){
          sh "sed -i 's#replace#hassansaaid/devsecops:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "pwd"
          sh "./kubectl --kubeconfig=kubeconfig.yml apply -f k8s_deployment_service.yaml"
        }
      }
    }
    }
  
}
