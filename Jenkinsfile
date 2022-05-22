pipeline{
    agent any
    stages{
        stage("Checkout"){
            steps{
                echo "========executing checkout========"
                git url:"https://github.com/cloud-junction/devops-webapp-maven.git", branch:"master"
            }
                       
        }
        stage("Unit Test"){
            steps{
                sh "mvn test"
            }
        }
        stage("SonarAnalysis"){
             steps{
                 sh "mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=mini-project"
             }

        }
        stage("Build Package"){
            steps{
                sh "mvn package"
            }
        }
        stage("Publish Nexus"){
            steps{
                nexusPublisher nexusInstanceId: 'Nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/my-app.war']], mavenCoordinate: [artifactId: 'my-app', groupId: 'com.mycompany.app', packaging: 'war', version: '1.0']]]
            }
        }
        stage("Approval"){
            steps{
                   timeout(time: 15, unit: 'MINUTES'){ 
	               input message: 'Do you approve deployment for production?' , ok: 'Yes'}
            }
        }
        stage("Deploy"){
            steps{
                 sshPublisher(publishers: [sshPublisherDesc(configName: 'prod-server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/my-app.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }

    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
            mail to:"cloud.junction.in.3@gmail.com",
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result FAILURE "
        }
    }
}
