pipeline{
   agent any
   tools{
       maven 'maven3'
   }
   /*triggers{
     //pollSCM('* * * * *')
     cron('* * * * *')
    }
    */
    stages{
        stage("Checkout"){
            steps{
               
                git url: 'https://github.com/cloud-junction/devops-webapp-maven.git',branch: 'master', credentialsId: 'github2'
               //checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github2', url: 'https://github.com/cloud-junction/devops-webapp-maven.git']]]
            }
        }
        stage("Unit Test"){
            steps{
                   sh 'mvn test'
            }
        }
        stage("Sonar Analysis"){
            steps{
                sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=cloudjunction'
            }
        }
        /*stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
          */
        stage("Build"){
            steps{
                sh 'mvn clean package'
            }
            
        }
        stage("Publish to Nexus"){
            steps{
               nexusPublisher nexusInstanceId: 'nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/my-app.war']], mavenCoordinate: [artifactId: 'my-app', groupId: 'com.mycompany.app', packaging: 'war', version: '1.0']]]
            }
        }
        stage("Deploy Dev"){
            steps{
                echo "Hello from Deploy stage"
            }

        }
        stage("Approval"){
            steps{
                    timeout(time: 15, unit: 'MINUTES'){ 
	               input message: 'Do you approve deployment for production?' , ok: 'Yes'

            }
        }
        }
        stage("Deploy Prod"){
            steps{
                sshPublisher(publishers: [sshPublisherDesc(configName: 'dev-server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/my-app.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
    post{
        failure {
                mail to:"cloud.junction.in.3@gmail.com",
             subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
             body: "${env.BUILD_URL} has result FAILURE " 

        }
    }
    
   
}
