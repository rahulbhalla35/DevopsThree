pipeline{
    agent any
    stages{
        stage("Begin - Code Checkout"){
            steps{
                checkout scm
            }
        }
        stage("Build Code"){
            steps{
                sh "mvn install"
            }
        }
        stage("Perform Unit Test"){
            steps{
                sh "mvn test"
            }
        }   
        stage("Perform Sonar Analysis"){
            steps{
                withSonarQubeEnv("Test_Sonar")
                {
                    sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar"
                }
            }
        }
        
        stage("Upload to Artifactory"){
            
            steps{
                sh "mvn deploy"
                rtPublishBuildInfo(
                    serverId: '123456789@artifactory',
                        )
            }
        }
        
        stage('Docker - Build Image'){
            steps{
                sh "docker build -t devopsassignmentimage:${BUILD_NUMBER} ."
            }
        }
        
        stage('Stop & Remove Container if found running'){
            steps{
               sh 'docker ps -f name=devopsassignmentcontainer -q | xargs --no-run-if-empty docker container stop'
               sh 'docker container ls -a -fname=devopsassignmentcontainer -q | xargs -r docker container rm'
            }
        }
        
        stage('Docker - Deployment'){
            steps{
                sh "docker run --name devopsassignmentcontainer -d -p 9050:8080 devopsassignmentimage:${BUILD_NUMBER}"
            }
        }
    }
}
