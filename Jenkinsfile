pipeline {
    agent { label 'server1-basirecsam' }

    stages {
        stage('Pull SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/basirecsam/simple-apps.git'
            }
        }
        
        stage('Build') {
            steps {
                sh'''
                cd apps
                npm install
                '''
            }
        }
        
        stage('Testing') {
            steps {
                sh'''
                cd apps
                npm test
                npm run test:coverage
                '''
            }
        }
        
        stage('Code Review') {
            steps {
                sh'''
                cd apps
                sonar-scanner \
                    -Dsonar.projectKey=simple-apps-basir \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://172.23.14.51:9000 \
                    -Dsonar.login=sqp_cac5298d3800c11f06c145f61afcc93f8c780d1c 
                '''
            }
        }
        
        stage('Change file .env') {
            steps {
                sh'''
                cd apps
                sed -i 's/localhost/db/g' .env
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh'''
                docker compose up --build -d
                '''
            }
        }
        
        stage('Backup') {
            steps {
                 sh 'docker compose push' 
            }
        }
    }
}