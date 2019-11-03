pipeline {

  environment {
    PROJECT = "crafty-mile-241013"
    APP_NAME = "test-app"
    CLUSTER = "standard-cluster-1"
    CLUSTER_ZONE = "us-central1-a"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
    GIT_DOCKER_URL = "https://github.com/saravana1992/test_gcr.git"
    GIT_DOCKER_CREDENTIALS_ID = "29ef3999-bdc8-4db7-8c00-53370e295c07"
    GIT_DOCKER_BRANCH = "master"
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
          /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=gcr.io/crafty-mile-241013/helm:latest
          '''
        }
      }
    }
    stage('Helm Deploy') {
      steps {
        container('helm') {
          sh ''' helm upgrade -f `pwd`/helloworld-chart/values.yml -i hello-world --namespace jenkins helloworld-chart/templates/nginx-ingress'''
        }
      }
    }
  }
}