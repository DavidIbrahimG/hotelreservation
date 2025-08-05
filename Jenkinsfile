pipeline {
    agent {
        docker {
            image 'google/cloud-sdk:latest'
            args '-u 0' // Run container as root to allow apt installs
        }
    }

    environment {
        VENV_DIR = '.venv'
        GCP_PROJECT = 'flash-district-467713-g8'
    }

    stages {
        stage('Setup and Install Dependencies') {
            steps {
                sh '''
                apt-get update && \
                apt-get install -y python3 python3-pip python3-venv

                rm -rf ${VENV_DIR}
                python3 -m venv ${VENV_DIR}
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
