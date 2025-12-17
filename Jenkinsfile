pipeline {
    agent any

    environment {
        DEPLOY_PATH = "/home/bala-murugan/Downloads/myproject"
        VENV_PATH   = "venv"
        DJANGO_PORT = "8003"
    }

    stages {

        stage('Prepare Workspace') {
            steps {
                sh """
                mkdir -p $DEPLOY_PATH
                """
            }
        }

        stage('Clone Repository') {
            steps {
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
                    python3 -m venv $VENV_PATH
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
                # Copy files to local folder
                rsync -av --delete \
                    --exclude $VENV_PATH \
                    --exclude .git \
                    ${WORKSPACE}/repo/ $DEPLOY_PATH/
                echo "Deployment completed to $DEPLOY_PATH"
                """
            }
        }

        stage('Run Django Server') {
            steps {
                dir("$DEPLOY_PATH") {
                    sh """
                    # Create virtualenv in deploy folder if missing
                    if [ ! -d "$VENV_PATH" ]; then
                        python3 -m venv $VENV_PATH
                    fi

                    . $VENV_PATH/bin/activate

                    # Upgrade pip and install requirements
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt

                    # Kill previous server if running
                    pkill -f "manage.py runserver $DJANGO_PORT" || true

                    # Start server on port 8003
                    nohup python manage.py runserver 0.0.0.0:$DJANGO_PORT > django.log 2>&1 &
                    echo "Django server started on port $DJANGO_PORT"
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
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
