pipeline {
    agent any

    tools {
        nodejs 'NodeJS-7.8.0'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checkout branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:v1.0 .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                        docker stop nodemain || true
                        docker rm nodemain || true
                        docker run -d --name nodemain --expose 3000 -p 3000:3000 nodemain:v1.0
                        '''
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh '''
                        docker stop nodedev || true
                        docker rm nodedev || true
                        docker run -d --name nodedev --expose 3001 -p 3001:3000 nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
