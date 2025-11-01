pipeline {
    agent any
    tools {
      jdk 'java-11'       
    }
    stages {
        stage('Build') {
            steps {
                sh 'java -version'
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
}
