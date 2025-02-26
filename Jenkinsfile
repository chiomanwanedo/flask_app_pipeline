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

        stage('Configure Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Configuring Systemd Service =================="
                sudo tee /etc/systemd/system/flask.service <<EOF
                [Unit]
                Description=Flask Application
                After=network.target

                [Service]
                User=ubuntu
                WorkingDirectory=/var/lib/jenkins/workspace/FlaskApp
                ExecStart=/usr/bin/python3 app.py
                Restart=always

                [Install]
                WantedBy=multi-user.target
                EOF
                sudo systemctl daemon-reload
                sudo systemctl enable flask
                '''
            }
        }

        stage('Start Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Restarting Flask Service =================="
                sudo systemctl restart flask
                sudo systemctl status flask --no-pager
                '''
            }
        }
    }
}
