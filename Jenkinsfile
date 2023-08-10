pipeline {
    agent any
    tools {
        maven 'maven-3.8.5'
    }
    options { 
        /*skipDefaultCheckout */
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '1'))
    
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
                sh 'mvn test -B -ntp'
            }
            post {
                always {
                   echo 'Mostrando resultado del proceso de test:'
                }
                success {
                   echo 'success'
                   junit 'target/surefire-reports/*.xml'
                   jacoco()
                }
                failure {
                   echo 'failure'
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package -DskipTests -B -ntp'
            }
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar -B -ntp'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Artifactory') {
            steps {
                script {
                    def release = 'spring-petclinic-rest-release'
                    def snapshot = 'spring-petclinic-rest-snapshot'

                    def server = Artifactory.server 'artifactory'
                    def rtMaven = Artifactory.newMavenBuild()

                    rtMaven.deployer server: server, releaseRepo: release, snapshotRepo: snapshot

                    def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install -B -ntp -DskipTests'
                    server.publishBuildInfo buildInfo
                }
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            deleteDir()
        }
        /*
        always {
            deleteDir()
        }
        */
    }
}