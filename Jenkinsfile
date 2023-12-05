pipeline {
    agent any
    tools {
        jdk 'openjdk-11.0.21.0.9-2'
    }
    stages {
        // stage('Check Env') {
        //     steps {
        //         sh '''
        //             env | grep -e PATH -e JAVA_HOME
        //             which java
        //             java -version
        //         '''
        //     }
            
        // }
        stage('Test') {
            steps {
                sh 'chmod +x mvnw'
                sh '''
                    ./mvnw clean test
                '''
            }
        }
        stage('Package') {
            steps {
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.package.type=uber-jar
                '''
            }
        }
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
                    // artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    // artifactExists = fileExists artifactPath;
                }
            }
        }
    }
}