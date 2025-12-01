pipeline {
    agent any
    environment {
        DBT_ENV = 'prod'
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Install deps') {
            steps {
                sh 'pip install dbt-core dbt-snowflake'
            }
        }
        stage('DBT: run tests') {
            steps {
                sh 'dbt deps'
                sh 'dbt seed --profiles-dir ./ --project-dir ./'
                sh 'dbt test --profiles-dir ./ --project-dir ./'
            }
        }
        stage('DBT: run') {
            steps {
                sh 'dbt run --profiles-dir ./ --project-dir ./'
            }
        }
        stage('Tag & Notify') {
            steps {
                sh 'git tag -a deploy-${env.BUILD_ID} -m "dbt deploy ${env.BUILD_ID}"'
                sh 'git push --tags'
            }
        }
    }
    post {
        success { echo 'DBT deploy succeeded' }
        failure { echo 'DBT deploy failed' }
    }
}
