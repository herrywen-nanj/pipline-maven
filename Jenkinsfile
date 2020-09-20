pipeline {
    agent any                                 
    stages {
        stage('build') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
        }
            steps {
                    sh 'mvn -B -DskipTests clean package'
                }
        }
        stage('Test') {
            steps {
                sh 'echo "test"'
            }
        }
	stage('Deploy') {
	    agent {
                node {
                    label 'master'
                    customWorkspace '/opt/'
                }
            }
	    steps {
		GIT_COMMIT_EMAIL = sh (script: 'ls -l',returnStdout: true).trim()
	    }
	    steps {
                sh 'echo ${GIT_COMMIT_EMAIL}'
            }
        }
    }  
}
