pipeline {
    agent {
            node {
                label 'master'
                customWorkspace '/ltzx/'
            }
        }
    environment { 
        IMAGE_REPO = 'registry.cn-hangzhou.aliyuncs.com/houzigang/jenkins_pre'
        Docker_registry = credentials('AliYun_Registry')
        DINGTALK_CREDS = credentials('dingTalk')
    }                              
    stages {
        stage('build') {
            agent {
            docker {
                image 'registry.cn-hangzhou.aliyuncs.com/houzigang/maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
		args '-w /ltzx'
                }
            }
            steps {
                sh 'mvn  -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'echo "excute sonarqube"'
            }
        }
        stage('login docker registry') {
            steps {
                retry(2) {sh 'docker login -u ${Docker_registry_USR} -p ${Docker_registry_PSW} registry.cn-hangzhou.aliyuncs.com'}
            }
        }
        stage('build image') {
            steps {
                retry(2) {sh 'docker build -t ${IMAGE_REPO}:${GIT_COMMIT} .'}
            }
        }
        stage('push image') {
            steps {
                retry(2) {sh 'docker push ${IMAGE_REPO}:${GIT_COMMIT}'}
            }
        }
        stage('modify yaml') {            
            steps {
                sh "sed -i 's#{{IMAGE_URL}}#${IMAGE_REPO}:${GIT_COMMIT}#g' deployment_${JOB_NAME}.yaml"
            }        
        }
        stage('Remote Deploy k8s cluster application') {
            steps {
                timeout(time: 1, unit: 'MINUTES') { sh "kubectl apply -f deployment_${JOB_NAME}.yaml" }
            }
        }
        
    }
    post {
	success { 
            echo 'Congratulations!' 
            sh """
                curl 'https://oapi.dingtalk.com/robot/send? access_token=${DINGTALK_CREDS_PSW}' -H 'Content-Type: application/json' -d '{"msgtype": "text","text": { "content": "😄👍构建成功👍😄\n 关键字：天宫云\n 项目名称: ${JOB_BASE_NAME}\n Commit Id: ${GIT_COMMIT}\n 构建地址：${RUN_DISPLAY_URL}" } 
                }' 
                """ 
            }
        failure { 
            echo 'Oh no!' 
            sh  """
                curl 'https://oapi.dingtalk.com/robot/send? access_token=${DINGTALK_CREDS_PSW}' -H 'Content-Type: application/json' -d '{"msgtype": "text", "text": {"content": "😖❌构建失败❌😖\n 关键字：天宫云\n 项目名称: ${JOB_BASE_NAME}\n Commit Id: ${GIT_COMMIT}\n 构建地址：${RUN_DISPLAY_URL}"} }' 
                """ 
                }
        always { 
            echo 'I will always say Hello again!' 
            }
    }
}
