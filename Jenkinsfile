pipeline {
    agent {
        node {
          label 'gradle7'
        }
    }
    
    parameters {
      string(name:'TAG_NAME',defaultValue: '',description:'')
    }

    options{
      buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
      HARBOR_HOST = '==='
      HARBOR_NAMESPACE = '==='
      APP_NAME = 'pos-fed-customer'
      GITLAB_URL = 'http://192.168.13.78/pos-fed-group/android/pos-fed-customer'
      WX_WORK_TOKEN = 'f260721c-b544-4a3b-9972-96a3c4db66744'
  }

    stages {
        stage ('checkout scm') {
            steps {
                checkout(scm)
        script {
          sh 'wget http://192.168.13.78/paas-pub/pipeline/-/raw/master/script/notify-qywx.py'
          sh 'wget http://192.168.13.78/paas-pub/pipeline/-/raw/master/script/send_to_wechat.py'
        }
      }
    }

        stage('Build'){
            when{
               branch 'pipeline-test'
           } 

            steps {
                // 构建 apk
                sh 'chmod +x ./gradlew'
                sh './gradlew clean'
                sh './gradlew assembleRelease'
            }
           post {
            aborted {
            sh 'python `pwd`/notify-qywx.py $APP_NAME $HARBOR_NAMESPACE $GITLAB_URL 发布取消 $WX_WORK_TOKEN'
            }
            success {
            sh 'python `pwd`/notify-qywx.py $APP_NAME $HARBOR_NAMESPACE $GITLAB_URL 发布成功 $WX_WORK_TOKEN'
            }
            failure {
            sh 'python `pwd`/notify-qywx.py $APP_NAME $HARBOR_NAMESPACE $GITLAB_URL 发布失败 $WX_WORK_TOKEN'
         }
        }           
      }                
    }
   } 