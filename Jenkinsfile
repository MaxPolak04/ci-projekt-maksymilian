pipeline {
    agent any

    stages {
        stage('Info') {
            steps {
                echo 'Numer builda: ${env.BUILD_NUMBER}'
                echo 'Gałąź: ${env.GIT_BRANCH}'
            }
        }
        stage('Testy') {
            when {
                expression {
                    env.GIT_BRANCH != 'origin/main'
                }
            }
            steps {
                echo 'Uruchomienie testów'
                sh 'python3 test_app.py'
            }
        }
        stage('Build') {
            steps {
                echo 'Budowanie obrazu'
                sh 'docker image build -t flask_app_image -f Dockerfile .'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy aplikacji na serwer'

                echo 'Usuwanie starego kontenera'
                sh 'docker container stop flask_app || true'

                echo 'Uruchomienie kontenera'
                sh 'docker run -p 5000:5000 -d --name=flask_app flask_app_image'
            }
        }
    }

    post {
        always {
            echo 'Pipeline zakończony...'
        }
        success {
            echo '...i przeszedł pomyślnie! Yeeeeeyy!'
        }
        failure {
            echo '...i coś się spiepszyło :c Patrz w logi.'
        }
    }

}