pipeline{
    agent any
    tools{
        maven 'maven3'
    }
    /*
    triggers{
        //cron('* * * * *')
    }
    */
    stages{
        stage("Checkout"){
            steps{
                //git url: 'https://github.com/cloud-junction/devops-webapp-maven.git', branch: 'master', credentialsId: 'github2'
                checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github2', url: 'https://github.com/cloud-junction/devops-webapp-maven.git']]]
                sh 'ls -ll'
            }
        }
        stage("Unit Test"){
            steps{
                sh 'mvn test'
            }
        }
        stage("Sonarqube Analysis"){
            steps{
                sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=devops-demo1'
            }
        }

        stage("Build"){
            steps{
                sh 'mvn package'
            }
        }
        stage("ArchiveArtifacts"){
            steps{
                archiveArtifacts artifacts: 'target/my-app.war', followSymlinks: false
            }
        }
        stage("PublishNexus"){
            steps{
                nexusPublisher nexusInstanceId: 'nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/my-app.war']], mavenCoordinate: [artifactId: 'my-app', groupId: 'com.mycompany.app', packaging: 'war', version: '2.0']]]
            }
        }
        stage("DeployDev"){
            steps{
                echo "Deploy Dev"
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
