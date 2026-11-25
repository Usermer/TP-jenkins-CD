pipeline {
    agent any

    environment {
        PYTHON_DIR = 'C:\\Users\\Pc\\AppData\\Local\\Programs\\Python\\Python311'
        PATH = "${PYTHON_DIR};${PYTHON_DIR}\\Scripts;${env.PATH}"

        NEXUS_URL  = 'http://localhost:8081/repository/python-release/Release.zip'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'fd1c26ae-6590-46ec-942e-119fb06734d5'
    }

    stages {

        stage('Nettoyage du workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Telechargement du fichier depuis Nexus') {
            steps {
                bat 'curl --fail -u %NEXUS_USER%:%NEXUS_PASS% -o Release.zip %NEXUS_URL%'
            }
        }

        stage('Verification du telechargement') {
            steps {
                bat '''
                if exist Release.zip (
                    echo Fichier telecharge avec succes
                ) else (
                    echo Echec du telechargement
                    exit /b 1
                )
                '''
            }
        }

        stage('Decompression de Release') {
            steps {
                bat '''
                powershell -Command "Expand-Archive -Path Release.zip -DestinationPath Release -Force"
                '''
            }
        }

        stage('Verification de la decompression') {
            steps {
                bat '''
                if exist Release\\calculator.py (
                    echo Decompression reussie
                ) else (
                    echo Echec de la decompression
                    exit /b 1
                )
                '''
            }
        }

        stage('Deploiement DEV') {
            steps {
                bat '''
                if not exist C:\\DEV mkdir C:\\DEV
                xcopy Release\\* C:\\DEV /E /I /Y
                '''
            }
        }

        stage('Verification DEV') {
            steps {
                bat '''
                echo ----- CONTENU DEV -----
                dir C:\\DEV
                if exist C:\\DEV\\calculator.py (
                    python C:\\DEV\\calculator.py
                )
                '''
            }
        }

        stage('Deploiement TEST') {
            steps {
                bat '''
                if not exist C:\\TEST mkdir C:\\TEST
                xcopy Release\\* C:\\TEST /E /I /Y
                '''
            }
        }

        stage('Tests fonctionnels') {
            steps {
                bat '''
                if exist C:\\TEST\\calculator.py (
                    python C:\\TEST\\calculator.py
                ) else (
                    echo calculator.py non trouve dans le repertoire TEST
                    exit /b 1
                )
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'Release.zip', fingerprint: true, allowEmptyArchive: true
        }
        success {
            echo '================================'
            echo 'Deploiement termine avec succes.'
            echo '================================'
        }
        failure {
            echo '================================'
            echo 'Deploiement echoue.'
            echo '================================'
        }
    }
}