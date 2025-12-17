pipeline {
agent {
node {
label 'any'
customWorkspace '/home/bala-murugan/Downloads/myproject'
}
}

```
environment {
    VENV_PATH = "venv"
}

stages {

    stage('Clone Repository') {
        steps {
            deleteDir()   // clean old files
            checkout scm
        }
    }

    stage('Create Virtualenv & Install Dependencies') {
        steps {
            sh '''
            python3 -m venv $VENV_PATH
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
}

post {
    success {
        echo "✅ Project cloned & CI completed in /home/bala-murugan/Downloads/myproject"
    }
    failure {
        echo "❌ Pipeline failed"
    }
}
```

}
