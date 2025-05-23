pipeline {
    agent any

    tools {
        nodejs 'Nodejs'  
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Divz-ops/aws-three-tier-web-app.git'
            }
        }

        stage('Build React App') {
            steps {
                dir('application-code/web-tier') {
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
                '''

               
                sh 'sudo cp application-code/nginx.conf /etc/nginx/nginx.conf'

                
                sh '''
                    sudo rm -rf /var/www/html/*
                    sudo cp -r application-code/web-tier/build/* /var/www/html/
                    ls -l /var/www/html
                '''

                
                sh '''
                    sudo nginx -t
                    sudo systemctl restart nginx
                    sudo systemctl enable nginx
                '''
            }
        }

        stage('Deploy Backend App') {
            steps {
                dir('application-code/app-tier') {
                    sh '''
                       
                        curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
                        sudo apt-get install -y nodejs

                        
                        sudo npm install -g pm2

                        
                        npm install

                       
                        pm2 start index.js
                        pm2 save
                    '''
                }
            }
        }
    }
}
