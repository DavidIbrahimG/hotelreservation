pipeline {
    agent {
        docker {
            image 'google/cloud-sdk:slim'
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
                # Install Python & pip
                apt-get update && \
                apt-get install -y python3 python3-pip python3-venv curl apt-transport-https gnupg lsb-release

                # Install Google Cloud SDK
                echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" > /etc/apt/sources.list.d/google-cloud-sdk.list
                curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
                apt-get update && apt-get install -y google-cloud-sdk

                # Set up Python virtual environment
                rm -rf ${VENV_DIR}
                python3 -m venv ${VENV_DIR}
                . ${VENV_DIR}/bin/activate

                # Upgrade pip and install project dependencies
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

        stage('Deploy to Google Cloud Run') {
            steps {
                withCredentials([file(credentialsId: 'GCP-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    export PATH=$PATH:${GCLOUD_PATH}

                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}

                    gcloud config set project ${GCP_PROJECT}

                    gcloud run deploy ml-project \
                        --image=gcr.io/${GCP_PROJECT}/ml-project:latest \
                        --platform=managed \
                        --region=us-central1 \
                        --allow-unauthenticated
                    '''
                }
            }
        }
    }
}
