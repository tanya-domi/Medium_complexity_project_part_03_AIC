pipeline {
    agent any
      tools {
        jfrog 'jfrog-cli'
      }
    stages {
        stage('snyk scan') {
            steps {
                snykSecurity(
                    snykInstallation: 'snyk@latest',
                    snykTokenId: 'snyk_api_token',
                    monitorProjectOnBuild: false,
                    failOnIssues: false,  // Use boolean for failOnIssues
                    additionalArguments: '--json-file-output=all-vulnerabilities.json'
                )
            }
        }
        stage('maven build artifact') {
            steps {
              // jf 'mvn-config --petclinic_artifact-libs-release --petclinic_artifact-libs-snapshot --petclinic_artifact-libs-release-local --petclinic_artifact-libs-snapshot-local'


                 sh 'mvn clean package -DskipTests=true '  // Correct capitalization for -DskipTests
            }
        }
       stage('building docker image') {
            steps {
                sh "docker build -t dts81/petclinic:${BUILD_NUMBER} ."
            }
        }
        stage('docker image push') {
            steps {
                withDockerRegistry(credentialsId: 'dockercred', url: '') {
                    sh "docker push dts81/petclinic:${BUILD_NUMBER}"
                }
            }
        }
        stage('Publish build info') {
            steps {
                jf 'rt build-publish'
            }
        }
       stage('push artifact') {
            steps {
              jf 'rt u /var/lib/jenkins/workspace/pipeline_part2/target/spring-petclinic-3.4.0-SNAPSHOT.jar  petclinic_artifact-libs-snapshot-local/springpetclinic/spring-petclinic-3.4.0-SNAPSHOT.jar'
            }
        }

    }
}
