pipeline {
    agent any

    environment {
        COMPOSE_FILE = "compose.yaml"
    }

    stages {

        stage('Clean Old Docker Resources') {
            steps {
                script {
                    echo "🔍 Stopping and removing existing containers…"
                    sh """
                        docker compose down --remove-orphans
                    """

                    echo "🧹 Removing dangling images…"
                    sh """
                        docker image prune -af
                    """

                    echo "🧽 Removing unused volumes & networks…"
                    sh """
                        docker volume prune -f
                        docker network prune -f
                    """
                }
            }
        }

        stage('Start Application') {
            steps {
                script {
                    echo "🚀 Starting docker-compose stack…"
                    sh """
                        docker compose up -d
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed. Check logs for any errors."
        }
    }
}
