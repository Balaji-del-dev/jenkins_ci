pipeline {
    agent any

    environment {
        DEPLOY_PATH = "/home/bala-murugan/Downloads/myproject"
        VENV_PATH   = "venv"
        DJANGO_PORT = "8003"  // Updated port
    }

    stages {

        stage('Prepare Workspace') {
            steps {
                // Make sure folder exists and Jenkins has permission
                sh """
                mkdir -p $DEPLOY_PATH
                """
            }
        }

        stage('Clone Repository') {
            steps {
                // Clone repo into Jenkins workspace, not directly into DEPLOY_PATH
                dir("${WORKSPACE}/repo") {
                    deleteDir()
                    git branch: 'main',
                        url: 'https://github.com/Balaji-del-dev/jenkins_ci.git'
                }
            }
        }

        stage('Setup Virtual Environment & Install Dependencies') {
            steps {
                dir("${WORKSPACE}/repo") {
                    sh """
                    # Create virtualenv safely
                    python3 -m venv $VENV_PATH

                    # Activate venv and upgrade pip
                    . $VENV_PATH/bin/activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                    """
                }
            }
        }

        stage('Run Django Tests') {
            steps {
                dir("${WORKSPACE}/repo") {
                    sh """
                    . $VENV_PATH/bin/activate
                    python manage.py test
                    """
                }
            }
        }

        stage('Deploy to Local Path') {
            steps {
                sh """
                # Copy project files to your local folder
                rsync -av --delete \
                    --exclude $VENV_PATH \
                    --exclude .git \
                    ${WORKSPACE}/repo/ $DEPLOY_PATH/
                echo "Deployment completed to $DEPLOY_PATH"
                """
            }
        }

        stage('Run Django Server (Optional)') {
            steps {
                dir("$DEPLOY_PATH") {
                    sh """
                    . $VENV_PATH/bin/activate || true

                    # Kill previous server if running
                    pkill -f "manage.py runserver" || true

                    # Start Django server in background
                    nohup python manage.py runserver 0.0.0.0:$DJANGO_PORT > django.log 2>&1 &
                    echo "Django server started at port $DJANGO_PORT"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for errors."
        }
    }
}
