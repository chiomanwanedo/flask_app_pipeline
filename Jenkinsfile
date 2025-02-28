pipeline {
    agent any

    environment {
        APP_DIR = "/var/lib/jenkins/workspace/Flask_app_deployment"
        VENV_DIR = "${APP_DIR}/venv"
    }

    stages {
        stage('Clone Repository') {
            steps {
                deleteDir()
                git branch: 'main',
                    url: 'https://github.com/chiomanwanedo/flask_rest_api.git'
                sh "ls -lart"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                echo "================= Installing Dependencies =================="

                # Install Python3, pip, and venv if missing
                sudo apt update -y
                sudo apt install -y python3 python3-pip python3-venv

                # Create Virtual Environment if it doesn't exist
                if [ ! -d "venv" ]; then
                    python3 -m venv venv
                fi

                # Install Flask and other dependencies
                ${VENV_DIR}/bin/pip install --upgrade pip
                ${VENV_DIR}/bin/pip install -r requirement.txt
                '''
            }
        }

        stage('Configure Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Configuring Flask as Systemd Service =================="

                SERVICE_FILE="/etc/systemd/system/flask_app.service"

                sudo bash -c "cat > $SERVICE_FILE <<EOF
                [Unit]
                Description=Flask Application
                After=network.target

                [Service]
                User=jenkins
                WorkingDirectory=${APP_DIR}
                Environment=VIRTUAL_ENV=${VENV_DIR}
                ExecStart=${VENV_DIR}/bin/python3 ${APP_DIR}/app.py
                Restart=always

                [Install]
                WantedBy=multi-user.target
                EOF"

                sudo systemctl daemon-reload
                sudo systemctl enable flask_app.service
                '''
            }
        }

        stage('Start Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Starting Flask Application =================="
                sudo systemctl restart flask_app.service
                sudo systemctl status flask_app.service --no-pager
                '''
            }
        }
    }
}
