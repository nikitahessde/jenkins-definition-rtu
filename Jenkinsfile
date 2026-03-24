pipeline {
    agent any
    environment {
        PATH = "/opt/homebrew/bin:/usr/local/bin:${env.PATH}"
    }
    triggers {
        pollSCM('*/1 * * * *')
    }
    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    installPipDeps()
                }
            }
        }
        stage('deploy-to-dev') {
            steps {
                script {
                    deployGreetings('dev', 7001)
                }
            }
        }
        stage('tests-on-dev') {
            steps {
                script {
                    testGreetings('dev')
                }
            }
        }
        stage('deploy-to-stg') {
            steps {
                script {
                    deployGreetings('stg', 7002)
                }
            }
        }
        stage('tests-on-stg') {
            steps {
                script {
                    testGreetings('stg')
                }
            }
        }
        stage('deploy-to-preprod') {
            steps {
                script {
                    deployGreetings('preprod', 7003)
                }
            }
        }
        stage('tests-on-preprod') {
            steps {
                script {
                    testGreetings('preprod')
                }
            }
        }
        stage('deploy-to-prod') {
            steps {
                script {
                    deployGreetings('prod', 7004)
                }
            }
        }
        stage('tests-on-prod') {
            steps {
                script {
                    testGreetings('prod')
                }
            }
        }
    }
}

def installPipDeps() {
    echo 'Installing all required dependencies..'
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'
    sh 'ls -la'
    sh 'python3 -m venv venv'
    sh './venv/bin/python -m pip install -r requirements.txt'
    echo 'Dependencies successfully installed..'
}

def deployGreetings(String envName, int port) {
    echo "Deployment to ${envName} has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'
    sh 'ls -la'
    sh 'python3 -m venv venv'
    sh './venv/bin/python -m pip install -r requirements.txt'
    sh "pm2 delete greetings-app-${envName} || true"
    sh "pm2 start app.py --name greetings-app-${envName} --interpreter ./venv/bin/python --port ${port}"
    echo "Deployment to ${envName} finished.."
}

def testGreetings(String envName) {
    echo "Testing Greetings API on ${envName} has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/course-js-api-framework.git'
    sh 'npm install'
    sh "npm run greetings greetings_${envName}"
    echo "Testing Greetings API on ${envName} finished.."
}
