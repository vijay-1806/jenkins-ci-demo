pipeline {

    agent {
        docker {
            image 'jenkins-docker-agent:1.0'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    stages {

        stage('Environment Check') {
            steps {
                sh 'python3 --version'
                sh 'docker --version'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'

                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install --no-cache-dir -r requirements.txt
                    python3 -m py_compile app.py
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application...'

                sh '''
                    . .venv/bin/activate
                    python3 -c "import app; print(app.health())"
                '''
            }
        }

        stage('Package') {
            steps {
                echo 'Creating artifact...'

                sh '''
                    tar -czf app.tar.gz \
                    app.py \
                    requirements.txt
                '''

                archiveArtifacts \
                    artifacts: 'app.tar.gz', \
                    fingerprint: true
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building application Docker image...'

            sh '''
                 docker build \
                -t jenkins-python-demo:${BUILD_NUMBER} .
        '''
    }
}
    }

    post {
        success {
            echo 'Docker Agent CI completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
