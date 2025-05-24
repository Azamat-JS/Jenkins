pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
        PORT = '4001'
    }

    tools {
        nodejs 'NodeJS 18'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/yourusername/yourrepo.git'
            }
        }

        stage('Deploy Code') {
            environment {
                MONGO_URI = credentials('MONGO_URI')
            }
            steps {
                sh '''
                rsync -av --exclude=".git" --exclude=".env" ./ /var/www/nodeapp
                cd /var/www/nodeapp
                echo "PORT=$PORT" > .env
                echo "MONGO_URI=$MONGO_URI" >> .env
                npm install
                npm run build || echo "No build step defined"
                pm2 restart nodeapp || pm2 start app.js --name nodeapp
                pm2 save
                '''
            }
        }
    }

    post {
        failure {
            echo 'Build failed!'
        }
    }
}
