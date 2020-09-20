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
            }
        }
        stage('Replace deployment field') {
            steps {
                update_image = sh(script: "cat deployment_myweb.yaml | grep -w image | awk '{print $NF}'", returnStdout: true).trim()
            }
        }
        stage ('deploy to remote tomcat') {
            steps {
		sh 'echo ${update_image}'
            }
        }
    }
}
