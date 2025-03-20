pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: "nodejs-builder"
spec:
  containers:
  - name: nodejs
    image: node:18-bullseye
    command:
    - cat
    tty: true
    resources:
      limits:
        memory: "2Gi"
        cpu: "2"
      requests:
        memory: "1Gi"
        cpu: "1"
    volumeMounts:
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
    - mountPath: "/home/jenkins/.npm-global"
      name: "npm-global"
  volumes:
  - emptyDir: {}
    name: workspace-volume
  - emptyDir: {}
    name: npm-global
"""
        }
    }

    tools {
        nodejs 'Node18'
    }

    environment {
        PATH = "/home/jenkins/.npm-global/bin:$PATH"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Check Node & NPM') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }

        stage('Install Angular CLI') {
            steps {
                sh 'npm install -g @angular/cli'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Angular App') {
            steps {
                sh 'ng build --configuration=production'
            }
        }

        stage('Linting Check') {
            steps {
                script {
                    def lintResult = sh(script: 'ng lint', returnStatus: true)
                    if (lintResult != 0) {
                        echo "⚠️ Linting có lỗi nhưng không dừng pipeline."
                    } else {
                        echo "✅ Linting không có lỗi."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build và kiểm tra hoàn thành thành công!"
        }
        failure {
            echo "❌ Pipeline thất bại. Kiểm tra logs để fix lỗi."
        }
    }
}
