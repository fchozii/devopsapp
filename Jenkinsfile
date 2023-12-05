pipeline {
    agent any
    stages {
        stage('Check Env') {
            steps {
                sh '''
                    env | grep -e PATH -e JAVA_HOME
                    which java
                    java-version
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