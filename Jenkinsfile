#!groovy

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
    stage('Docker Push') {
      agent any
      steps {
          sh "docker login -u arjunarveti -p 127b5c6e-c73e-4075-af8d-aef8abddf6ea"
          sh 'docker push arjunarveti/spring-petclinic:latest'
      }
    }
    stage('Deploy Spring Petclinic app') {
      steps {
      build 'deploy-spring-petclinic-app'
      }
    }
  }
}
