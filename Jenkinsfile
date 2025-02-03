pipeline {
  agent any
  environment {
  Java_Home = tool name: 'java-17', type: 'jdk'
  }
  stages {
      stage('Snyk Test using plugin') {
            steps {
                snykSecurity(
                    snykInstallation: 'snyk@latest',
                    snykTokenId: 'snyk_api_token',
                    monitorProjectOnBuild: false,
                    failOnIssues: 'false',
                    additionalArguments: '--json-file-output=all-vulnerabilities.json'
                )
            }
      }
      stage('Build Artifact') {
            steps {
              withMaven(maven: 'maven') {
              sh "mvn clean package -DskipTests=true -Dcheckstyle.skip"
              archive 'target/*.jar'
              }
            }
       }
      stage('Test Maven - JUnit') {
            steps {
              withMaven(maven: 'maven') {
              sh "mvn test -Dcheckstyle.skip"
              }
            }
            post{
              always{
                junit 'target/surefire-reports/*.xml'
              }
            }
        }
       stage('Sonarqube Analysis - SAST') {
            steps {
                withSonarQubeEnv(installationName: 'SonarCloud', credentialsId: 'SONAR_TOKEN') {
                withMaven(maven: 'maven'){
                sh "mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=spring-petclinic02 -Dcheckstyle.skip"
                }
                }
              }
        }
        stage('Docker Image creation') {
            steps {
              withDockerRegistry(credentialsId: 'dockercred', url: '') {
              sh "docker build -t petclinic_img ."
	      sh "docker tag petclinic_img:latest tds81/images:petclinic_img"
              sh "docker push tds81/images:petclinic_img"
                sh "docker run -d -p 8080:8080 petclinic_img"
              }
            }
       }
     }
}
