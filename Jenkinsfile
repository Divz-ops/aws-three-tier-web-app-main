pipeline {
    agent any

    tools {
        nodejs 'Nodejs'
    }

    environment {
        WEB_DIR = 'application-code/web-tier'
        APP_DIR = 'application-code/app-tier'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-token', branch: 'main', url: 'https://github.com/Divz-ops/aws-three-tier-web-app-main.git'
            }
        }

        stage('Build React App') {
            steps {
                dir("${WEB_DIR}") {
                    sh '''
                        npm install
                        npm run build
                        ls -l build
                    '''
                }
            }
        }

        stage('Deploy to Nginx') {
            steps {
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y nginx

                    # Replace default Nginx config with custom one
                    sudo cp application-code/nginx.conf /etc/nginx/nginx.conf

                    # Clear existing HTML and copy built React files
                    sudo rm -rf /var/www/html/*
                    sudo cp -r application-code/web-tier/build/* /var/www/html/

                    # Test and reload Nginx
                    sudo nginx -t
                    sudo systemctl restart nginx
                    sudo systemctl enable nginx
                '''
            }
        }

        stage('Deploy Backend App') {
            steps {
                dir("${APP_DIR}") {
                    sh '''
                        # Install Node.js 16 if not present
                        curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
                        sudo apt-get install -y nodejs

                        # Install PM2 for process management
                        sudo npm install -g pm2

                        # Install dependencies and start the app with PM2
                        npm install
                        pm2 start index.js --name backend-app
                        pm2 save
                    '''
                }
            }
        }
    }
}
