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
        stage('Build Artefact') {
            steps {
                /*sh 'ls -la'*/
                sh 'mvn clean package -DskipTests -B -ntp'
                /*bat (Para Windows)*/
            }
        }
    }
}