pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'rg-jenkins'
        APP_SERVICE_NAME = 'webapijenkinskhushveer457'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/Khushveer01/python-webapp.git'
            }
        }

        stage('Set Up Python') {
            steps {
                sh 'python -m venv venv'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Test App') {
            steps {
                sh '. venv/bin/activate && python app.py &'
                sh 'sleep 5 && curl http://127.0.0.1:5000'
            }
        }

        stage('Login to Azure') {
            steps {
                withCredentials([azureServicePrincipal("${AZURE_CREDENTIALS_ID}")]) {
                    sh '''
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                        az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                sh 'az webapp up --name $APP_NAME --resource-group $RESOURCE_GROUP --runtime "PYTHON:3.9" --sku B1'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
