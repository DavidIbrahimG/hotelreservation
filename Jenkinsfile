pipeline {
    agent any  // Run on Jenkins host

    environment {
        VENV_DIR = '.venv'
        GCP_PROJECT = 'flash-district-467713-g8'
    }

    stages {
        stage('Setup and Install Dependencies') {
            agent {
                docker {
                    image 'google/cloud-sdk:latest'
                    args '-u 0'  // Run as root to allow apt installs
                }
            }
            steps {
                sh '''
                echo "Installing Python and Dependencies..."
                apt-get update && \
                apt-get install -y python3 python3-pip python3-venv

                echo "Creating virtual environment..."
                rm -rf ${VENV_DIR}
                python3 -m venv ${VENV_DIR}
                . ${VENV_DIR}/bin/activate

                echo "Upgrading pip and installing requirements..."
                pip install --upgrade pip
                pip install -r requirements.txt || true
                '''
            }
        }

        stage('Build and Push Docker Image to GCR') {
            steps {
                withCredentials([file(credentialsId: 'GCP-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    echo "Authenticating with GCP..."
                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                    gcloud config set project ${GCP_PROJECT}
                    gcloud auth configure-docker --quiet

                    echo "Building Docker image..."
                    docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .

                    echo "Pushing Docker image to GCR..."
                    docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                    '''
                }
            }
        }
    }
}
