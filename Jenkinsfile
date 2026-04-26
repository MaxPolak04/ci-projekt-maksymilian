pipeline {
    agent any

    environment {
        APLIKACJA = "FlaskApp"
        WERSJA = "1.0.0"
    }

    options {
        timeout(time: 20, unit: "MINUTES")
    }

    parameters {
        choice (//
            name: "SRODOWISKO",
            choices: ["dev", "staging", "prod"],
            description: "Wybierz srodowisko docelowe"
        )
    }

    stages {
        stage("Info") {
            steps {
                echo "Aplikacja: ${env.APLIKACJA}"
                echo "Wersja: ${env.WERSJA}"
                echo "Numer builda: ${env.BUILD_NUMBER}"
                echo "Zadanie: ${env.JOB_NAME}"
                echo "Workspace: ${env.WORKSPACE}"
                echo "Gałąź: ${env.GIT_BRANCH}"
            }
        }
        stage("Testy") {
            when {
                expression {
                    env.GIT_BRANCH != "origin/main"
                }
            }
            steps {
                echo "Uruchomienie testów"
                sh "python3 test_app.py"
            }
        }
        stage("Build") {
            steps {
                echo "Budowanie obrazu"
                sh "docker image build -t flask_app_${env.BUILD_NUMBER} -f Dockerfile ."
            }
        }
        stage("Analiza jakości") {
            steps {
                echo "sprawdzanie plików"
                echo "skanowanie obrazu"
            }
        }
        stage("Wdrożenie DEV") {
            when {
                expression { params.SRODOWISKO == "dev" }
            }
            steps {
                echo "Wdrozenie na DEV"
            }
        }
        stage("Zatwierdzenie") {
            options {
                timeout(time: 5, unit: "MINUTES")
            }
            when {
                expression { params.SRODOWISKO == "prod" }
            }
            steps {
                input message: "Czy na pewno wdrozyc na PRODUKCJE?",
                ok: "Tak, wdrazaj!"
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploy aplikacji na serwer, SRODOWISKO: ${params.SRODOWISKO}"


                echo "Usuwanie starego kontenera"
                sh "docker container rm -f flask_app || true"

                echo "Uruchomienie kontenera"
                sh "docker run -p 5000:5000 -d --name=flask_app flask_app_${env.BUILD_NUMBER}"

                echo "Wdrozenie zakonczone"
            }
        }
    }

    post {
        always {
            echo "Pipeline zakończony..."
        }
        success {
            echo "...i przeszedł pomyślnie! Yeeeeeyy!"
            echo "SRODOWISKO: ${params.SRODOWISKO}"
            echo "BUILD_NUMBER: ${env.BUILD_NUMBER}"
        }
        failure {
            echo "...i coś się spiepszyło :c Patrz w logi."
        }
    }

}