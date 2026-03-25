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

        stage('Detect Branch') {
            steps {
                script {
                    def ref = env.GIT_BRANCH ?: ""
                    echo "Raw branch: ${ref}"

                    env.BRANCH_NAME = ref.replace("origin/", "").replace("refs/heads/", "")
                    echo "Detected Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Checkout Code') {
            steps {
                script {
                    git branch: "${env.BRANCH_NAME}",
                        url: 'https://github.com/avikashrana63-source/attendence-project.git'
                }
            }
        }

        stage('Build (Show Changes)') {
            steps {
                echo "🔨 Building project..."
            
                sh "echo '===== index.html content ====='"
                sh "cat index.html"
                sh "echo '==============================='"
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
                                chown -R ${USER}:${USER} /var/www/main
                            '
                            scp index.html ${USER}@${SERVER}:/var/www/main/
                            """
                        }

                    } else if (env.BRANCH_NAME == "dev") {

                        echo " Deploying DEV..."

                        sshagent(['server-ssh']) {
                            sh """
                            ssh ${USER}@${SERVER} '
                                rm -rf /var/www/dev &&
                                mkdir -p /var/www/dev &&
                                chown -R ${USER}:${USER} /var/www/dev
                            '
                            scp index.html ${USER}@${SERVER}:/var/www/dev/
                            """
                        }

                    } else if (env.BRANCH_NAME == "prefix") {

                        echo " PREFIX branch detected"
                        echo " Build completed"
                        echo " Changes shown above in logs"
                        echo " Deployment skipped"

                    } else {

                        echo " No rule for this branch"
                    }
                }
            }
        }
    }
}
