#!groovy
def spClientId = env.CLIENT_ID
def spClientSecret = env.CLIENT_SECRET
def spTenantId = env.TENANT_ID

pipeline {
  agent any
  stages {
    stage('Maven Install') {
      agent {
        docker {
          image 'maven:3.5.0'
        }
      }
      steps {
        sh 'mvn clean install'
      }
    }
    stage('Docker Build') {
      agent any
      steps {
        sh 'docker build -t arjunarveti/spring-petclinic:latest .'
      }
    }
    stage('Azure login') {
        steps{
          wrap([$class: 'MaskPasswordsBuildWrapper', varMaskRegexes: [[regex: '[0-9a-fA-F]{8}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{12}'], [regex: '\\b[a-zA-Z0-9]{16}\\b']]]) {
          sh  "az login --service-principal --username ${spClientId} --password ${spClientSecret} --tenant ${spTenantId} --allow-no-subscriptions"
        }
      }
    }
    stage('Docker Push') {
      steps{
          script {
          wrap([$class: 'MaskPasswordsBuildWrapper', varMaskRegexes: [[regex: '[0-9a-fA-F]{8}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{4}\\-[0-9a-fA-F]{12}']]]) {
          def username = sh(returnStdout: true, script: 'az keyvault secret show --vault-name "test-ee-devops-vault" --name "secret-docker-username" --query value|tr -d \'"\'').trim()
          def password = sh(returnStdout: true, script: 'az keyvault secret show --vault-name "test-ee-devops-vault" --name "secret-docker-password" --query value|tr -d \'"\'').trim()
          sh "docker login -u ${username} -p ${password}"
          sh "docker push ${username}/spring-petclinic:latest"
          }
        }
      }
    }
    stage('Deploy Spring Petclinic app') {
      steps {
      build 'deploy-spring-petclinic-app'
      }
    }
  }
}
