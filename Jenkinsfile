pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'chmod +x app.sh'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application...'
                sh './app.sh'
            }
        }

        stage('Package') {
            steps {
                echo 'Creating artifact...'
                sh 'tar -czf app.tar.gz app.sh'
            }
        }
    }

    post {
        success {
            echo 'CI pipeline completed successfully!'
        }

        failure {
            echo 'CI pipeline failed!'
        }
    }
}
