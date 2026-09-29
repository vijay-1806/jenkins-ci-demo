pipeline {
    agent any

    stages {

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
                echo 'Packaging application...'
                sh 'tar -czf app.tar.gz app.sh'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
