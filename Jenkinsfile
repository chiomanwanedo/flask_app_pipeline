pipeline {
    agent any

    environment {
        FLASK_ENV = 'production'
        FLASK_APP = 'app.py'
    }

    stages {
        stage('Clone Repository') {
            steps {
                deleteDir()
                git branch: 'main', url: 'https://github.com/chiomanwanedo/flask_rest_api.git'
                sh "ls -lart"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                echo "================= Installing Dependencies =================="
                sudo apt update -y
                sudo apt install -y python3 python3-pip
                pip3 install --upgrade pip
                pip3 install -r requirements.txt
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                echo "================= Starting Flask Application =================="
                nohup python3 app.py > flask.log 2>&1 &
                '''
            }
        }
    }
}
