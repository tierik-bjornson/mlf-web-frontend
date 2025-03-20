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
  volumes:
  - emptyDir: {}
    name: workspace-volume
"""
        }
    }

    tools {
        nodejs 'Node18'
    }

    environment {
        PATH = "/home/jenkins/agent/.npm-global/bin:$PATH"
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

        stage('Run Unit Tests') {
            steps {
                sh 'ng test --watch=false --browsers=ChromeHeadless'
            }
        }

        stage('Linting Check') {
            steps {
                sh 'ng lint'
            }
        }
    }

    post {
        success {
            echo "✅ Build and tests passed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check the logs."
        }
    }
}
