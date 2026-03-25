pipeline {
    agent any

    environment {
        SERVER = "192.168.10.85"
        USER = "abhi1worker"
        REPO = "https://github.com/avikashrana63-source/attendence-project.git"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Process All Branches') {
            steps {
                script {

                    def branches = ["main", "dev", "prefix"]

                    for (branch in branches) {

                        echo "================================="
                        echo "Processing branch: ${branch}"
                        echo "================================="

                        deleteDir()

                        // Checkout branch
                        git branch: branch, url: "${REPO}"

                        // Simple Build (no file content)
                        echo "Build successful for ${branch}"

                        // Deploy logic
                        if (branch == "main") {

                            echo "Deploying MAIN"

                            sshagent(['server-ssh']) {
                                sh """
                                ssh -o StrictHostKeyChecking=no ${USER}@${SERVER} '
                                    rm -rf /var/www/main || true
                                    mkdir -p /var/www/main
                                '

                                scp -o StrictHostKeyChecking=no -r * ${USER}@${SERVER}:/var/www/main/
                                """
                            }

                        } else if (branch == "dev") {

                            echo "Deploying DEV"

                            sshagent(['server-ssh']) {
                                sh """
                                ssh -o StrictHostKeyChecking=no ${USER}@${SERVER} '
                                    rm -rf /var/www/dev || true
                                    mkdir -p /var/www/dev
                                '

                                scp -o StrictHostKeyChecking=no -r * ${USER}@${SERVER}:/var/www/dev/
                                """
                            }

                        } else if (branch == "prefix") {

                            echo "Prefix branch - build only, no deployment"

                        }
                    }
                }
            }
        }
    }
