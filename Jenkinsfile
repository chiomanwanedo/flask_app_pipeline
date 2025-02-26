pipeline {
    agent any

    environment {
        APP_DIR = "/var/lib/jenkins/workspace/Flask_app_deployment"
        VENV_DIR = "${APP_DIR}/venv"
    }

    stages {
        stage('Clone Repository') {
            steps {
                deleteDir()  // Clean workspace
                git branch: 'main',
                    url: 'https://github.com/chiomanwanedo/flask_rest_api.git'
                sh "ls -lart"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                echo "================= Installing Dependencies =================="
                
                # Update and Install Required Packages
                sudo apt update -y
                sudo apt install -y python3 python3-pip python3-venv

                # Create Virtual Environment if it doesn't exist
                if [ ! -d "venv" ]; then
                    python3 -m venv venv
                fi

                # Activate Virtual Environment
                source venv/bin/activate

                # Upgrade pip inside Virtual Environment
                pip install --upgrade pip

                # Install Flask and other dependencies from requirements file
                pip install -r requirement.txt

                # Deactivate Virtual Environment
                deactivate
                '''
            }
        }

        stage('Configure Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Configuring Flask as Systemd Service =================="

                SERVICE_FILE="/etc/systemd/system/flask_app.service"

                # Create Flask Systemd Service File
                sudo bash -c "cat > $SERVICE_FILE <<EOF
                [Unit]
                Description=Flask Application
                After=network.target

                [Service]
                User=jenkins
                WorkingDirectory=${APP_DIR}
                Environment=VIRTUAL_ENV=${VENV_DIR}
                ExecStart=${VENV_DIR}/bin/python ${APP_DIR}/app.py
                Restart=always

                [Install]
                WantedBy=multi-user.target
                EOF"

                # Reload Systemd
                sudo systemctl daemon-reload

                # Enable the Service to Start on Boot
                sudo systemctl enable flask_app.service
                '''
            }
        }

        stage('Start Flask as Systemd Service') {
            steps {
                sh '''
                echo "================= Starting Flask Application =================="

                # Restart Flask Service
                sudo systemctl restart flask_app.service

                # Check Flask Service Status
                sudo systemctl status flask_app.service --no-pager
                '''
            }
        }
    }
}
