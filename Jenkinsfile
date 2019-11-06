pipeline {

  environment {
    PROJECT = "crafty-mile-241013"
    APP_NAME = "test-app"
    NAMESPACE = "jenkins"
    CLUSTER = "standard-cluster-1"
    CLUSTER_ZONE = "us-central1-a"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
    GIT_URL = "https://github.com/saravana1992/test_gcr.git"
    GIT_CREDENTIALS_ID = "29ef3999-bdc8-4db7-8c00-53370e295c07"
    GIT_BRANCH = "master"
    HELM_TEMPLATE = "helloworld-chart"
    HELM_VALUE_FILE = "/helloworld-chart/values.yaml"
    HELM_NAME = "hello-world"
    DEPLOYMENT_NAME = "hello-world-helloworld-chart"
    SLACK_COLOR_DANGER  = '#E01563'
    SLACK_COLOR_INFO    = '#6ECADC'
    SLACK_COLOR_WARNING = '#FFC300'
    SLACK_COLOR_GOOD    = '#3EB991'
    SLACK_CHANNEL_1 = 'testjenkins'
  }

  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: git
    image: gcr.io/cloud-builders/git
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
  - name: helm
    image: gcr.io/crafty-mile-241013/helm1:latest
    command:
    - cat
    tty: true
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - cat
    tty: true
    volumeMounts:
      - name: kaniko-secret
        mountPath: /secret
    env:
      - name: GOOGLE_APPLICATION_CREDENTIALS
        value: /secret/kaniko-secret.json
  volumes:
   - name: kaniko-secret
     secret:
       secretName: kaniko-secret   
"""
}
  }
  stages {
      stage('Build and push image with Container Builder') {
        steps {
          container(name: 'kaniko', shell: '/busybox/sh') {
            sh '''#!/busybox/sh
            /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=${IMAGE_TAG}
            '''
          }
        }
      }
      stage('Helm Deploy') {
        steps {
          container('helm') {
            sh '''
            helm template -f `pwd`$HELM_VALUE_FILE $HELM_TEMPLATE
            helm upgrade -f `pwd`$HELM_VALUE_FILE -i $HELM_NAME --namespace $NAMESPACE $HELM_TEMPLATE'''
          }
        }
      }
      stage('Validate deployment') {
        steps {
          container('kubectl') {
            sh '''
            kubectl rollout status deployment $DEPLOYMENT_NAME --namespace $NAMESPACE'''
          }
        }
      }
    }



  post {
  aborted {

    echo "Sending message to Slack"
    slackSend (color: "${env.SLACK_COLOR_WARNING}",
               channel: "${env.SLACK_CHANNEL_1}",
               message: "*ABORTED:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}")
  } // aborted

  failure {

    echo "Sending message to Slack"
    slackSend (color: "${env.SLACK_COLOR_DANGER}",
               channel: "${env.SLACK_CHANNEL_1}",
               message: "*FAILED:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}")
  } // failure

  success {
    echo "Sending message to Slack"
    slackSend (color: "${env.SLACK_COLOR_GOOD}",
               channel: "${env.SLACK_CHANNEL_1}",
               message: "*SUCCESS:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}")
  } // success

  } // post
}