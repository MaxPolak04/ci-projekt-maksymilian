pipeline {
    agent any

    stages {
        stage('Info') {
            steps {
                echo 'Numer builda: ${env.BUILD_NUMBER}'
                echo 'Numer builda: ${env.GIT_BRANCH}'
            }
        }
        stage('Testy') {
            steps {
                echo 'Uruchomienie testów'
                sh 'python3 test_app.py'
            }
        }
        stage('Build') {
            steps {
                echo 'Budowanie obrazu'
                sh 'docker image build -f Dockerfile .'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy aplikacji na serwer'

                echo 'Usuwanie starego kontenera'
                sh 'docker container stop flask_app'

                echo 'Uruchomienie kontenera'
                sh 'docker run -p 5000:5000 -d -n flask_app'
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