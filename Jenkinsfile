pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: '236c5fa7-2164-4a22-bdeb-3abd1be1d7ed', \
                    url: 'https://github.com/SilesiaEagle/abcd-student', \
                    branch: 'main'
                }
            }
        }
        stage('Welcome message') {
            steps {
                echo 'Hello Maciek!'
            }
        }

        stage('Prepare') {
            steps {
                sh 'mkdir -p results'
            }
        }

        stage('DAST') {
            steps {
                sh '''
                container_name="juice-shop"

                # Sprawdź, czy kontener już istnieje
                if [ "$(docker ps -a -q -f name=$container_name)" ]; then
                echo "Kontener $container_name istnieje."

                # Sprawdź, czy kontener jest w statusie "running"
                if [ "$(docker inspect -f '{{.State.Status}}' $container_name)" = "running" ]; then
                    echo "Kontener $container_name już działa."
                else
                    echo "Kontener $container_name istnieje, ale nie jest uruchomiony. Uruchamiam..."
                    docker start $container_name
                fi
                else
                echo "Kontener $container_name nie istnieje. Tworzę nowy kontener..."
                docker run --name $container_name -d --rm -p 3000:3000 bkimminich/juice-shop
                fi

                # Pętla czekająca na status "running"
                while [ "$(docker inspect -f '{{.State.Status}}' $container_name)" != "running" ]; do
                echo "Czekam, aż kontener $container_name będzie w trybie 'running'..."
                sleep 1  # Sprawdza co 1 sekundę
                done

                echo "Kontener $container_name działa!"

                docker run --name zap \
                -v /mnt/c/Users/kosmi/_devsecops/abcd-student/.zap:/zap/wrk/:rw \
                -t ghcr.io/zaproxy/zaproxy:stable \
                bash -c "zap.sh -daemon -cmd -addonupdate && \
                        zap.sh -daemon -cmd -addoninstall communityScripts && \
                        zap.sh -cmd -quickurl http://host.docker.internal:3000 -quickout /zap/wrk/report.html -quickoutxml /zap/wrk/report.xml"

                '''
            }
        }
    }
}
