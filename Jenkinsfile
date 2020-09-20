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
		script {
			sh "cat /opt/deployment_myweb.yaml | grep -w image | awk '{print $NF}' > update_image-field"
			env.update_image-field = readFile("update_image-field").trim()
            }
        }
        stage ('deploy to remote tomcat') {
            steps {
		sh 'echo ${update_image-field}'
            }
        }
    }
}
}
