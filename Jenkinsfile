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

                    sh 'printenv'

                    // Forma 1:
                    def release = 'spring-petclinic-rest-release'
                    def snapshot = 'spring-petclinic-rest-snapshot'
                   
                    def server = Artifactory.server 'artifactory'
                    /*
                    def rtMaven = Artifactory.newMavenBuild()

                    rtMaven.deployer server: server, releaseRepo: release, snapshotRepo: snapshot

                    def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install -B -ntp -DskipTests'
                    server.publishBuildInfo buildInfo
                    */

                    //Forma 2:
                    def pom = readMavenPom file: 'pom.xml'

                    def uploadSpec = """ 
                        {
                            "files": [
                                {
                                    "pattern": "target/.*.jar",
                                    "target": "${pom.groupId}/${pom.artifactId}/${pom.version}/",
                                    "regexp": "true"
                                    
                                }
                            ]
                        }
                    """
                    /*
                    def uploadSpec = """ 
                        {
                            "files": [
                                {
                                    "pattern": "target/.*.jar",
                                    "target": "${release}/${BUILD_NUMBER}/",
                                    "regexp": "true"
                                    
                                }
                            ]
                        }
                    """
                    */
                    server.upload spec: uploadSpec

                    /* Interpolación de String en Groovy
                    sh 'echo hello world'
                    sh 'echo hello world'
                    sh 'echo hello world'
                    =
                    sh '''
                        echo dany
                        echo mitocode
                    '''
                    */
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