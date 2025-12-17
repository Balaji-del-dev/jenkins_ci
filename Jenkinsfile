pipeline {
    agent any

    environment {
        DEPLOY_PATH = "/home/bala-murugan/Downloads/myproject"
        VENV_PATH   = "venv"
        DJANGO_PORT = "8000"
    }

    stages {

        stage('Prepare Workspace') {
            steps {
                // Clean folder before clone
                sh """
                mkdir -p $DEPLOY_PATH
                # Make sure Jenkins already owns this folder manually
                """
            }
        }

        stage('Clone Repository') {
            steps {
                dir("$DEPLOY_PATH") {
                    // Delete old files
                    deleteDir()
                    // Clone GitHub repo
                    git branch: 'main',
                        url: 'https://github.com/Balaji-del-dev/jenkins_ci.git'
                }
            }
        }

        stage('Setup Virtual Environment & Install Dependencies') {
            steps {
                dir("$DEPLOY_PATH") {
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
                dir("$DEPLOY_PATH") {
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
                echo "Deployment completed to $DEPLOY_PATH"
                """
            }
        }

        stage('Run Django Server (Optional)') {
            steps {
                dir("$DEPLOY_PATH") {
                    sh """
                    . $VENV_PATH/bin/activate

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
