pipeline {
    agent any
  
      tools {
        maven 'maven3'
      }
      stages {
        // stage('snyk scan') {
        //     steps {
        //         snykSecurity(
        //             snykInstallation: 'snyk@latest',
        //             snykTokenId: 'SNYK_API_TOKEN',
        //             monitorProjectOnBuild: false,
        //             failOnIssues: false,  // Use boolean for failOnIssues
        //             additionalArguments: '--json-file-output=all-vulnerabilities.json'
        //         )
        //     }
        // }

        stage('maven build artifact') {
            steps {
                 sh "mvn clean package -DskipTests=true -Dcheckstyle.skip"  // Correct capitalization for -DskipTests
                //  archive 'target/*.jar'
            }
        }
        // stage('Test Maven - JUnit') {

        //     steps {
        //       withMaven(maven: 'maven') {
        //       sh "mvn test -Dcheckstyle.skip"
        //       }
        //     }
        //     post{
        //       always{
        //         junit 'target/surefire-reports/*.xml'
        //       }
        //     }
        // }
        // stage('Sonarqube Analysis -SAST') {
        //     steps {
        //         withSonarQubeEnv(installationName: 'SonarCloud', credentialsId: 'SONAR_TOKEN') {
        //         sh "mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=spring-petclinic02 -Dcheckstyle.skip"
        //         }
        //       }
        // }

        
        stage('building a docker image') {
            steps {
                sh "docker build -t tds81/petapp:${BUILD_NUMBER} ."
            }
        }
        stage ('Push to dockerhub') {
            steps {
                script {
                    echo 'Pushing to dockerhub'
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                            sh "echo $PASS | docker login -u $USER --password-stdin"
                            sh "docker push tds81/petapp:${BUILD_NUMBER}"
                      }
                }
            }  
        }

        // stage('docker image push') {
        //     steps {
        //         withDockerRegistry(credentialsId: 'dockercred', url: '') {
        //             sh "docker push tds81/petapp:${BUILD_NUMBER}"
        //         }
        //     }
        // }
        
    }
}



