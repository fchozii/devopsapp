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
    }
}