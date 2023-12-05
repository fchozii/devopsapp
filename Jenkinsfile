pipeline {
    agent {
        node {
            label 'Built-In Node'
        }
    }
    tools {
        jdk 'openjdk-11.0.21.0.9-2'
    }
    stages {
        stage('Check Env') {
            steps {
                sh '''
                    env | grep -e PATH -e JAVA_HOME
                    which java
                    java -version
                '''
            }
            
        }
        // stage('Test') {
        //     steps {
        //         sh '''
        //             ./mvnw clean test
        //         '''
        //     }
        // }
    }
}