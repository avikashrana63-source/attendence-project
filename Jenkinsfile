pipeline {
    agent any

    stages {

        stage('Detect Branch') {
            steps {
                echo "Branch is: ${env.BRANCH_NAME}"
            }
        }

        stage('Build') {
            steps {
                echo "Building project..."
            }
        }

        stage('Deploy Main') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                echo "Deploying to MAIN"
            }
        }

        stage('Deploy Dev') {
            when {
                expression { env.BRANCH_NAME == 'dev' }
            }
            steps {
                echo "Deploying to DEV"
            }
        }

        stage('Prefix Check') {
            when {
                expression { env.BRANCH_NAME == 'prefix' }
            }
            steps {
                echo "Only checking PREFIX"
            }
        }

    }
}
