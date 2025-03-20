pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: nodejs-builder
spec:
  containers:
  - name: nodejs
    image: node:18
    command:
    - cat
    tty: true
    resources:
      requests:
        memory: "1Gi"
        cpu: "1"
      limits:
        memory: "2Gi"
        cpu: "2"
    volumeMounts:
      - mountPath: "/home/jenkins/agent"
        name: "workspace-volume"
  volumes:
  - emptyDir: {}
    name: "workspace-volume"
"""
        }
    }

    tools {
        nodejs 'Node18'
    }

    environment {
        PATH = "$HOME/.npm-global/bin:$PATH"
    }

    stages {
        stage('Check Network Connectivity') {
            steps {
                sh 'ping -c 3 google.com || echo "No internet connection"'
            }
        }

        stage('Install Angular CLI') {
            steps {
                retry(3) {
                    sh 'npm install -g @angular/cli'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                retry(3) {
                    sh 'npm install'
                }
            }
        }

        stage('Build') {
            steps {
                retry(3) {
                    sh 'ng build --configuration=production'
                }
            }
        }

        stage('Test') {
            steps {
                retry(3) {
                    sh 'ng test --watch=false --browsers=ChromeHeadless'
                }
            }
        }

        stage('Lint') {
            steps {
                retry(3) {
                    sh 'ng lint'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
