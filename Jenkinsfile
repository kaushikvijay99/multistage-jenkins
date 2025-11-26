pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh 'python3 -m venv venv'
                sh './venv/bin/pip install --upgrade pip'
                sh './venv/bin/pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh './venv/bin/python -m unittest discover -s .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                mkdir -p ${WORKSPACE}/python-app-deploy
                cp ${WORKSPACE}/app.py ${WORKSPACE}/python-app-deploy/
                cp -r ${WORKSPACE}/venv ${WORKSPACE}/python-app-deploy/
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Running application...'
                sh '''
                nohup ${WORKSPACE}/python-app-deploy/venv/bin/python \
                    ${WORKSPACE}/python-app-deploy/app.py \
                    > ${WORKSPACE}/python-app-deploy/app.log 2>&1 &
                echo $! > ${WORKSPACE}/python-app-deploy/app.pid
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing application...'
                sh './venv/bin/python test_app.py'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
