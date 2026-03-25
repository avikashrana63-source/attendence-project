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
            }
        }

        stage('Detect Branch from Webhook') {
            steps {
                script {
                    // GitHub sends branch like: refs/heads/dev
                    def ref = env.GIT_BRANCH ?: ""
                    echo "Raw branch: ${ref}"

                    env.BRANCH_NAME = ref.replace("origin/", "").replace("refs/heads/", "")
                    echo "Detected Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Checkout Correct Branch') {
            steps {
                script {
                    git branch: "${env.BRANCH_NAME}",
                        url: 'https://github.com/avikashrana63-source/attendence-project.git'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {

                    if (env.BRANCH_NAME == "main") {

                        echo "Deploying MAIN..."

                        sshagent(['server-ssh']) {
                            sh """
                            ssh ${USER}@${SERVER} '
                                rm -rf /var/www/main &&
                                mkdir -p /var/www/main &&
                                chown -R abhi1worker:abhi1worker /var/www/main
                            '
                            scp index.html ${USER}@${SERVER}:/var/www/main/
                            """
                        }

                    } else if (env.BRANCH_NAME == "dev") {

                        echo "Deploying DEV..."

                        sshagent(['server-ssh']) {
                            sh """
                            ssh ${USER}@${SERVER} '
                                rm -rf /var/www/dev &&
                                mkdir -p /var/www/dev &&
                                chown -R abhi1worker:abhi1worker /var/www/dev
                            '
                            scp index.html ${USER}@${SERVER}:/var/www/dev/
                            """
                        }

                    } else if (env.BRANCH_NAME == "prefix") {

                        error("❌ PREFIX branch not allowed")

                    } else {

                        echo "No deployment for this branch"
                    }
                }
            }
        }
    }
}
