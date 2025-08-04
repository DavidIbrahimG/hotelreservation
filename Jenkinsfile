pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
        }
    }

    environment {
        VENV_DIR = '.venv'  // stays inside the container, NOT on host
    }

    stages {
        stage('Setup and Install Dependencies') {
            steps {
                sh '''
                rm -rf ${VENV_DIR}
                python -m venv ${VENV_DIR}
                . ${VENV_DIR}/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt || true
                '''
            }
        }
    }
}
