pipeline {

    agent {
    docker {
        image 'jenkins-docker-agent:1.0'
        args '''
            -v /var/run/docker.sock:/var/run/docker.sock
            --group-add 986
            -v /home/ubuntu/.aws:/tmp/.aws:ro
        '''
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


        stage('Push to ECR') {
    steps {
        echo 'Logging into Amazon ECR...'

        sh '''
            export AWS_SHARED_CREDENTIALS_FILE=/tmp/.aws/credentials
            export AWS_CONFIG_FILE=/tmp/.aws/config

            aws sts get-caller-identity

            aws ecr get-login-password --region ap-south-1 | \
            docker login \
                --username AWS \
                --password-stdin \
                152439496947.dkr.ecr.ap-south-1.amazonaws.com
        '''

        sh '''
            docker tag \
                jenkins-python-demo:${BUILD_NUMBER} \
                152439496947.dkr.ecr.ap-south-1.amazonaws.com/jenkins-python-demo:${BUILD_NUMBER}
        '''

        sh '''
            docker push \
                152439496947.dkr.ecr.ap-south-1.amazonaws.com/jenkins-python-demo:${BUILD_NUMBER}
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
