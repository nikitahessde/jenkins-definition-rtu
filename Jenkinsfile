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
                    deploy('dev', 7001)
                }
            }
        }
        stage('tests-on-dev') {
            steps {
                script {
                    test('dev')
                }
            }
        }
        stage('deploy-to-stg') {
            steps {
                script {
                    deploy('stg', 7002)
                }
            }
        }
        stage('tests-on-stg') {
            steps {
                script {
                    test('stg')
                }
            }
        }
        stage('deploy-to-preprod') {
            steps {
                script {
                    deploy('preprod', 7003)
                }
            }
        }
        stage('tests-on-preprod') {
            steps {
                script {
                    test('preprod')
                }
            }
        }
        stage('deploy-to-prod') {
            steps {
                script {
                    deploy('prod', 7004)
                }
            }
        }
        stage('tests-on-prod') {
            steps {
                script {
                    test('prod')
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

def deploy(String envName, int port) {
    echo "Deployment to ${envName} has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'
    sh 'ls -la'
    sh 'python3 -m venv venv'
    sh './venv/bin/python -m pip install -r requirements.txt'
    sh "npx --yes pm2 delete greetings-app-${envName} || true"
    sh "npx --yes pm2 start app.py --name greetings-app-${envName} --interpreter ./venv/bin/python -- --port ${port}"
    echo "Deployment to ${envName} finished.."
}

def test(String envName) {
    echo "Testing Greetings API on ${envName} has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/course-js-api-framework.git'
    sh 'npm install'
    sh "npm run greetings greetings_${envName}"
    echo "Testing Greetings API on ${envName} finished.."
}
