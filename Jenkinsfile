pipeline {
   
   agent any

   stages {
      stage("Checkout"){

        steps{

            git url:"https://github.com/cloud-junction/devops-webapp-maven.git", branch:"master"
            
        }
      }

      stage("Unit Test"){

        steps{

            sh "mvn test"
        }
      }

      stage("Sonar Analysis"){
        steps{
            echo "Sonar Analysis"
           // sh "mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=devopsdemo123"
        }
      }
      stage("Build"){
        steps{
            sh "mvn package"
        }
      }



      stage("ArchiveArtifacts"){
        steps{
              archiveArtifacts artifacts: 'target/my-app.war', followSymlinks: false
        }
      }
      
      stage("PublishNexus"){

        steps{
            echo "Publish Nexus"
        }
      }

      stage("DeployDev"){

        steps{

            echo "Deploying Dev"
        }
      }

      stage("Approval"){

        steps{

            timeout(time: 15, unit: 'MINUTES'){ 
	               input message: 'Do you approve deployment for production?' , ok: 'Yes'}
        }
      }

      stage("DeployProd"){
        steps{
            sshPublisher(publishers: [sshPublisherDesc(configName: 'DevServer', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/my-app.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
        }
      }


   }

   post{
       always{
        echo "=============run always========="
       } 
       success{
        echo "==============run on only success"
       }
       failure{
        echo "===============Pipeline Execution failed=============="
        mail to: "cloud.junction.in@gmail.com",
        subject: "Status Pipeline:  ${currentBuild.fullDisplayName}",
        body: "${env.BUILD_URL} has result FAILURE"

       }     


   }
  

}
