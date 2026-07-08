pipeline {
    agent any

    environment {
        APP_NAME = "flask-practice-app"
        DEPLOY_DIR = "/tmp/flask-staging"
        VENV_DIR = "venv"
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building Flask application...'

                sh '''
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip

                    if [ -f requirements.txt ]; then
                        pip install -r requirements.txt
                    else
                        pip install flask pytest
                    fi
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'

                sh '''
                    . ${VENV_DIR}/bin/activate

                    if [ -d tests ]; then
                        pytest tests/
                    else
                        echo "No tests directory found. Running basic syntax check."
                        python3 -m compileall .
                    fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Flask application to staging...'

                sh '''
                    rm -rf ${DEPLOY_DIR}
                    mkdir -p ${DEPLOY_DIR}
                    cp -r . ${DEPLOY_DIR}/

                    echo "Application deployed to staging directory: ${DEPLOY_DIR}"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'

            emailext(
                subject: "SUCCESS: Jenkins Pipeline - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                Jenkins pipeline completed successfully.

                Job Name: ${env.JOB_NAME}
                Build Number: ${env.BUILD_NUMBER}
                Build URL: ${env.BUILD_URL}
                """,
                to: "mcrajmd@gmail.com"
            )
        }

        failure {
            echo 'Pipeline failed.'

            emailext(
                subject: "FAILED: Jenkins Pipeline - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                Jenkins pipeline failed.

                Job Name: ${env.JOB_NAME}
                Build Number: ${env.BUILD_NUMBER}
                Build URL: ${env.BUILD_URL}

                Please check Jenkins console logs for details.
                """,
                to: "mcrajmd@gmail.com"
            )
        }
    }
}
