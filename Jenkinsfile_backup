pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
                sh 'echo ${ENV_TYPE}'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage ('deploy to remote tomcat') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'b67257f4-2d8d-4870-9863-b3b98a87ba06', url: 'http://192.168.5.190:8080')], 
                contextPath: '/', war: '**/*.war'
            }
        }
    }
}
