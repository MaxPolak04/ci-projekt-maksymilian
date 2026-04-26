pipeline {
    agent any

    environment {
        APLIKACJA = 'FlaskApp'
        WERSJA = '1.0.0'
    }

    options {
        timeout(time: 15, unit: 'MINUTES')
    }

    parameters {
        choice {
            name: 'SRODOWISKO',
            choices: ['dev', 'staging', 'prod'],
            description: 'Wybierz srodowisko docelowe'
        }
    }

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
        stage('Zatwierdzenie') {
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            when {
                expression { params.SRODOWISKO == 'prod' }
            }
            steps {
                input message: 'Czy na pewno wdrozyc na PRODUKCJE?',
                ok: 'Tak, wdrazaj!'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy aplikacji na serwer, SRODOWISKO: ${params.SRODOWISKO}'

                echo 'Usuwanie starego kontenera'
                sh 'docker container rm -f flask_app || true'

                echo 'Uruchomienie kontenera'
                sh 'docker run -p 5000:5000 -d --name=flask_app flask_app_image'

                echo 'Wdrozenie zakonczone'
            }
        }
    }

    post {
        always {
            echo 'Pipeline zakończony...'
        }
        success {
            echo '...i przeszedł pomyślnie! Yeeeeeyy!'
            echo 'SRODOWISKO: ${params.SRODOWISKO}'
        }
        failure {
            echo '...i coś się spiepszyło :c Patrz w logi.'
        }
    }

}