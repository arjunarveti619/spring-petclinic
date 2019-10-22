pipeline {
    agent none
    stages {
        // stage('Checkout Project from Git'){
        //    git url: "https://github.com/arjunarveti619/spring-petclinic.git"
        // }
        stage('Maven install'){
           agent {
               docker {
                   image 'maven:3.5.0'
               }
           }
           steps {
               sh 'mvn clean install'
           }
        }
        stage('Docker Build'){
            agent any
            steps {
                sh 'docker build -t arjunarveti/spring-petclinic:latest'
            }
        }
    }
}
