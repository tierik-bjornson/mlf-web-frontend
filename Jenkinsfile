pipeline {
    agent any

    tools {
        nodejs 'Node18' 
    }

    environment {
        PATH = "$HOME/.npm-global/bin:$PATH"
    }

    stages {
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

        stage('Build') {
            steps {
                sh 'ng build --configuration=production'
            }
        }

        stage('Test') {
            steps {
                sh 'ng test --watch=false --browsers=ChromeHeadless'
            }
        }

        stage('Lint') {
            steps {
                sh 'ng lint'
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
