pipeline {
  environment {
    registry = "mpmohasin9/hello-world"
    registryCredential = 'dockerhub'
    dockerImage = ''
    //ECRURL = 'http://999999999999.dkr.ecr.eu-central-1.amazonaws.com'
    //ECRCRED = 'ecr:eu-central-1:tap_ecr'
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/sdfdsfsfd/node-app.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          // login to ECR - for now it seems that that the ECR Jenkins plugin is not performing the login as expected. I hope it will in the future.
                    //sh("eval \$(aws ecr get-login --no-include-email | sed 's|https://||')")
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
    stage('Login into AWS EKS') {
      steps{
        sh "aws eks --region us-west-2 update-kubeconfig --name cluster_name"
      }
    }
    stage('Run Ansible playbook to create deployment') {
      steps{
        sh "ansible-playbook deployment.yaml"
      }
    }
    stage('Run Ansible playbook to create service') {
      steps{
        sh "ansible-playbook service.yaml"
      }
    }
  }
}
