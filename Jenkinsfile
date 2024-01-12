pipeline {
    agent any
    tools {
        jdk 'openjdk-11.0.21'
        maven 'maven-3.6.3'
    }
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = 'nexus3'
        // This can be http or https
        NEXUS_PROTOCOL = 'https'
        // Where your Nexus is running. 'nexus-3' is defined in the docker-compose file
        NEXUS_URL = '192.168.56.7:8443'
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = 'devopsapp'
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = 'NEXUS_VDR02'
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    mvn clean compile
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    mvn test
                '''
            }
        }
        stage('Package') {
            steps {
                sh '''
                    mvn package -DskipTests \
                    -Dquarkus.package.type=uber-jar
                '''
            }
        }
        stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonar-scanner-4.8.1'
          }
          steps {
            withSonarQubeEnv('sonar-ce-9.9.3') {
               withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                sh '''
                        ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=devopsapp \
                        -Dsonar.projectName=shopping-cart \
                        -Dsonar.login=${SONAR_TOKEN} \
                        -Dsonar.projectVersion=1.0-SNAPSHOT \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/classes/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/
                    '''
               }
            }
          }
        }
/*        
        stage("Quality Gate") {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
*/
        stage('Publish') {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                     artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                     artifactExists = fileExists artifactPath;

                    if(artifactExists) {
                    echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                    
                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
                        version: pom.version,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            // Artifact generated such as .jar, .ear and .war files.
                            [artifactId: pom.artifactId,
                            classifier: '',
                            file: artifactPath,
                            type: pom.packaging],

                            // Lets upload the pom.xml file for additional information for Transitive dependencies
                            [artifactId: pom.artifactId,
                            classifier: '',
                            file: "pom.xml",
                            type: "pom"]
                        ]
                    );

                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}