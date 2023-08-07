pipeline {
    agent any
    tools {
        maven 'maven-3.8.5'
    }
    stages {
        // stage('Checkout') {
        //     steps {
        //         git branch: 'master',
        //         url: 'https://ideatechperu@bitbucket.org/ideatech_developer/petclinic-rest.git'
        //     }
        // }
        stage('Compile') {
            steps {
                /*sh 'ls -la'*/
                sh 'mvn clean compile -B -ntp'
                /*bat (Para Windows)*/
            }
        }
        stage('Test') {
            steps {
                /*sh 'ls -la'*/
                sh 'mvn test -B -ntp'
                /*bat (Para Windows)*/
            }
            post {
                always {
                  echo 'Mostrando resultado del proceso de test:'
                }
                success {
                  echo 'success'
                  jacoco()
                  junit 'target/surefire-reports/*.xml'
                }
                failure {
                  echjo 'failure'
                }
            }
        }
        stage('Build') {
            steps {
                /*sh 'ls -la'*/
                sh 'mvn package -DskipTests -B -ntp'
                /*bat (Para Windows)*/
            }
        }
    }
}