pipeline {
    parameters {
        string(name: 'MONGODB_URI_TEST', defaultValue: 'mongodb://localhost:27017/api-user-test', description: 'Put the URI Mongo database.')
        string(name: 'FLASK_ENV', defaultValue: 'testing', description: 'Enter some information about the person')
        booleanParam(name: 'DEBUG', defaultValue: true, description: 'Sets DEBUG option')
    }
    environment {
        PATH="/var/lib/jenkins/miniconda3/bin:$PATH"
    }
    options
    {
        skipDefaultCheckout(true)
        buildDiscarder(logRotator(numToKeepStr: '3', daysToKeepStr: '7'))
        timestamps()
    }
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Envs') {
            steps('Print Env After source checkout') {

                echo "Branch Name: ${env.BRANCH_NAME}"
                echo "BUILD_NUMBER : ${env.BUILD_NUMBER}"
                echo "BUILD_ID : ${env.BUILD_ID}"
                echo "JOB_NAME: ${env.JOB_NAME}"
                echo "BUILD_TAG : ${env.BUILD_TAG}"
                echo "EXECUTOR_NUMBER : ${env.EXECUTOR_NUMBER}"
                echo "NODE_NAME: ${env.NODE_NAME}"
                echo "NODE_LABELS : ${env.NODE_LABELS}"
                echo "WORKSPACE : ${env.WORKSPACE}"
                echo "JENKINS_HOME : ${env.JENKINS_HOME}"

            }
        }
        stage ("Install Dependencies") {
            steps {
                sh """
                conda create --yes -n ${JOB_NAME} python=3
                source activate ${JOB_NAME}
                pip install --upgrade pip
                pip install -r requirements/dev.txtt
                """

            }
        }
        stage('Run Tests') {
            steps {
                sh """
                export MONGODB_URI_TEST=${params.MONGODB_URI_TEST}
                export FLASK_ENV=${params.FLASK_ENV}
                export DEBUG=${params.DEBUG}
                source activate ${JOB_NAME}
                make test
                """
            }
        }
        stage('Unit Test') {
            steps('Unit Test') {
                echo "Running the unit test..."
            }
        }
        stage('Integration Test') {
            steps {
                echo "Running the integration test..."
            }
        }
        stage('Deploy stage') {
            when {
                branch 'master'
            }
            steps {
                echo 'Deploy master to stage'
            }
        }
    }
    post {
        always {
            sh 'conda remove --yes -n ${BUILD_TAG} --all'
        }
        success {
            echo "Success"
        }
        failure {
            echo "Send e-mail, when failed"
        }
    }
}
