pipeline {
  environment {
    SLACK_CHANNEL = '#foo,#bar'
  }
  agent {
    kubernetes {
      label 'saucelab'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
  vertical: "custo"
  ci: true
spec:
  containers:
  - name: maven
    image: "jamesdbloom/docker-java8-maven"
    command: ["cat"]
    env:
    - name: SAUCE_USERNAME
      valueFrom:
        secretKeyRef:
          name: sauce
          key: username
    - name: SAUCE_ACCESS_KEY
      valueFrom:
        secretKeyRef:
          name: sauce
          key: accesskey
    tty: true
"""
    }
  }
  stages {
    stage('build') {
      steps {
        container('maven') {
          sh '''#!/bin/bash
          mvn test-compile
          mvn test
          '''
          junit testDataPublishers: [[$class: 'SauceOnDemandReportPublisher']], testResults: '**/surefire-reports/*.xml'
        }
      }
    }
  }  
  post {
    failure {
      container('maven') {
          junit testDataPublishers: [[$class: 'SauceOnDemandReportPublisher']], testResults: '**/surefire-reports/*.xml'
      }
    }
  }
}
