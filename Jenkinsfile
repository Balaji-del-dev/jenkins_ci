pipeline {
    agent {
        node {
            label 'any'
            customWorkspace '/home/bala-murugan/Downloads/myproject'
        }
    }

    environment {
        VENV_PATH = "venv"
    }

    stages {

        stage('Clone Repository') {
            steps {
                deleteDir()
                checkout scm
            }
        }

        stage('Create Virtualenv & Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Django Tests') {
            steps {
                sh '''
                . venv/bin/activate
                python manage.py test
                '''
            }
        }
    }

    post {
        success {
            echo "CI completed successfully in /home/bala-murugan/Downloads/myproject"
        }
        failure {
            echo "CI failed"
        }
    }
}
