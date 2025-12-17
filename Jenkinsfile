pipeline {
    agent any

    environment {
        DEPLOY_PATH = "/home/bala-murugan/Downloads/myproject"
        VENV_PATH   = "venv"
        DJANGO_PORT = "8000"
    }

    stages {

        stage('Clone Repository') {
            steps {
                // Clean folder before cloning
                deleteDir()
                // Clone from GitHub
                git branch: 'main',
                    url: 'https://github.com/Balaji-del-dev/jenkins_ci.git'
            }
        }

        stage('Setup Virtual Environment & Install Dependencies') {
            steps {
                sh '''
                # Create virtualenv if not exists
                python3 -m venv $VENV_PATH
                # Activate and install dependencies
                . $VENV_PATH/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Django Tests') {
            steps {
                sh '''
                . $VENV_PATH/bin/activate
                python manage.py test
                '''
            }
        }

        stage('Deploy to Local Path') {
            steps {
                sh '''
                echo "Deploying to $DEPLOY_PATH..."

                # Create folder if not exists
                mkdir -p $DEPLOY_PATH

                # Copy project files excluding venv and git
                rsync -av --delete \
                  --exclude $VENV_PATH \
                  --exclude .git \
                  --exclude __pycache__ \
                  ./ $DEPLOY_PATH/

                echo "Deployment completed at $DEPLOY_PATH"
                '''
            }
        }

        stage('Run Django Server (Optional)') {
            steps {
                sh '''
                cd $DEPLOY_PATH
                . $VENV_PATH/bin/activate

                # Kill previous server if running
                pkill -f "manage.py runserver" || true

                # Start Django server in background
                nohup python manage.py runserver 0.0.0.0:$DJANGO_PORT > django.log 2>&1 &
                echo "Django server started at port $DJANGO_PORT"
                '''
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
