pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "ls"
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
        sh 'docker build . -t hassansaaid/devsecops:""$GIT_COMMIT""'
        sh 'docker push hassansaaid/devsecops:""$GIT_COMMIT""'
      }
      }
    }
    stage("DEPLOY ON K8S"){
      steps{
          sh "sed -i 's#reolace#hassansaaid/devsecops:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "pwd"
          sh "curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.26.4/2023-05-11/bin/linux/amd64/kubectl"
          sh "chmod +x ./kubectl"
          sh "mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH"
          sh "echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc"
          sh " kubectl version --short --client"
          sh "./kubectl --kubeconfig=kubeconfig.yml apply -f k8s_deployment_service.yaml"
      }
    }
    }
  
}
