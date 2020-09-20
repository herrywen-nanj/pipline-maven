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
		sh '''# !/bin/bash
		ENV_IMAGE = $(cat /opt/deployment_myweb.yaml | grep -w image | awk '{print $NF}')
                echo $ENV_IMAGE
		'''  
	    }
        }
    }  
}
