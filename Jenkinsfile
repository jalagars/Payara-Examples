pipeline {
   agent any
   tools {
      maven "Maven 3.6.3"
      jdk "default"
   }
   environment {
      gitrepo = "https://github.com/jalagars/Payara-Examples.git"
      artifactoryCredential = 'JAY_AWS_ARTIFACTORY_SERVER'
      artifactoryServerUrl = 'http://ec2-3-92-243-157.compute-1.amazonaws.com:8082/artifactory'
  }
   //added comment
   stages {
      stage('Build') {
         steps {
            // Get some code from a GitHub repository
            git "${env.gitrepo}"

            // Run Maven on a Unix agent.
            sh '''
            cd ./javaee/hello-world-rest
            mvn -Dmaven.test.failure.ignore=true clean package
            '''
            // To run Maven on a Windows agent, use
            // bat "mvn -Dmaven.test.failure.ignore=true clean package"
         }
      }
       stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "JAY_AWS_ARTIFACTORY_SERVER",
                    url: artifactoryServerUrl,
                    credentialsId: artifactoryCredential
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "JAY_AWS_ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "JAY_AWS_ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }
        stage ('Publish build info to Artifactory') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
   }
}
