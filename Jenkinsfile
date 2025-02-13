pipeline {
    agent any

    environment {
        EC2_USER = "ubuntu"                  // Change to your EC2 user (e.g., ec2-user for Amazon Linux)
        EC2_HOST = "13.235.238.42"      // Replace with your EC2 instance IP
        APP_DIR = "/home/ubuntu/USER-DETAILS/app" // Path to app directory on EC2
        SSH_KEY = "/path/to/your-key.pem"    // Path to your EC2 private key
        APP_PORT = "5000"                    // Flask application port
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    echo "Copying updated application files to EC2..."
                    sh """
                        scp -i ${SSH_KEY} -r app/* ${EC2_USER}@${EC2_HOST}:${APP_DIR}/
                    """

                    echo "Restarting application on EC2..."
                    sh """
                        ssh -i ${SSH_KEY} ${EC2_USER}@${EC2_HOST} << EOF
                            pkill -f "python" || true
                            nohup python3 ${APP_DIR}/main.py > ${APP_DIR}/app.log 2>&1 &
                            echo "Application restarted successfully!"
                        EOF
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to EC2 successful!'
        }
        failure {
            echo 'Deployment failed. Check the logs for details.'
        }
    }
}
