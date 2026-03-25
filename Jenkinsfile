pipeline {
    agent any

    environment {
        SERVER = "192.168.10.85"
        USER = "abhi1worker"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
                echo "Workspace cleaned"
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Detect Branch & Deploy') {
            steps {
                script {

                    def branch = sh(
                        script: "git rev-parse --abbrev-ref HEAD",
                        returnStdout: true
                    ).trim()

                    echo "Current Branch: ${branch}"

                    if (branch == "main") {

                        echo "Deploying MAIN..."

                        sshagent(['server-ssh']) {
                            sh """
                            ssh -o StrictHostKeyChecking=no ${USER}@${SERVER} '
                                rm -rf /var/www/main &&
                                mkdir -p /var/www/main &&
                                chown -R abhi1worker:abhi1worker /var/www/main
                            '

                            scp -o StrictHostKeyChecking=no index.html ${USER}@${SERVER}:/var/www/main/
                            """
                        }

                        echo "✅ MAIN deployed → http://${SERVER}:8001"

                    } else if (branch == "dev") {

                        echo "Deploying DEV..."

                        sshagent(['server-ssh']) {
                            sh """
                            ssh -o StrictHostKeyChecking=no ${USER}@${SERVER} '
                                rm -rf /var/www/dev &&
                                mkdir -p /var/www/dev &&
                                chown -R abhi1worker:abhi1worker /var/www/dev
                            '

                            scp -o StrictHostKeyChecking=no index.html ${USER}@${SERVER}:/var/www/dev/
                            """
                        }

                        echo "✅ DEV deployed → http://${SERVER}:8002"

                    } else if (branch == "prefix") {

                        error("❌ PREFIX branch not allowed for deployment")

                    } else {

                        echo "⚠️ Unknown branch — no deployment"
                    }
                }
            }
        }
    }
}
