pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // mount docker socket
        }
    }

    environment {
        VENV_DIR = '.venv'
        GCP_PROJECT = 'flash-district-467713-g8'
        GCLOUD_PATH = '/var/jenkins_home/google-cloud-sdk/bin'
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

        stage('Build and Push Docker Image to GCR') {
            steps {
                withCredentials([file(credentialsId: 'GCP-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    export PATH=$PATH:${GCLOUD_PATH}

                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}

                    gcloud config set project ${GCP_PROJECT}

                    gcloud auth configure-docker --quiet

                    docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .

                    docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                    '''
                }
            }
        }
    }
}
