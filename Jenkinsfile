pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
            GCP_PROJECT = "flash-district-467713-g8"
            GCLOUD_PATH = "/var/jenkins_home/google-cloud-sdk/bin"
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
        stage('Building and Pushing Docker Image to GCR'){
            steps{
                withCredentials([file(credentialsId: 'GCP-key' , variable : 'GOOGLE_APPLICATION_CREDENTIALS')]){
                    script{
                        echo 'Building and Pushing Docker Image to GCR.............'
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

    
}
