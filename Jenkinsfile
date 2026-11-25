pipeline{
    agent any
    environment {
        NEXUS_uRL="http://localhost:8081/#preview/browse/python-release/Release.zip"
        NEXUS_USER="admin"
        NEXUS_PASS="fd1c26ae-6590-46ec-942e-119fb06734d5"

    }
    stages{
        stage('Nettoyage du workspace'){
            steps{
                deleteDir()
            }
        }
        stage('Telecharhement du fichier depuis Nexus'){
            steps{
                bat '''
                 curl -u %NEXUS_USER%:%NEXUS_PASS% -O Release.zip %NEXUS_uRL%
                '''
            }
        }
        stage('verification du telechargement'){
            steps{
                bat '''
                if exist Release.zip (
                    echo "Fichier téléchargé avec succès"
                ) else (
                    echo "Échec du téléchargement"
                )
                '''
            }
        }
        stage('Decomposition du Release'){
            steps{
                bat '''
                powershell -Command "Expand-Archive -Path Release.zip -DestinationPath Release -Force"
                '''
            }
        }
        stage('Verification de la Decomposition'){
            steps{
                bat '''
                if exist Release (
                    echo "Decomposition réussie"
                ) else (
                    echo "Échec de la Decomposition"
                )
                '''
            }
        }
        stage('Depoiement DEV'){
            steps{
                bat '''
                if not exist C:\\DEV mkdir C:\\DEV
                xcopy Release\\* C:\\DEV /E /I /Y
                '''
            }
        }
        stage('Verification DEV'){
            steps{
                bat '''
                echo -----CONTENU DEV-----
                dir C:\\DEV
                if exist C:\\DEV\calculator.py(
                    python C:\\DEV\\calculator.py
                )
                '''
            }
        }
        stage('Depoiement TEST'){
            steps{
                bat '''
                if not exist C:\\TEST mkdir C:\\TEST
                xcopy Release\\* C:\\TEST /E /I /Y
                '''
            }
        }
        stage('Tests fonctionnels'){
            steps{
                bat '''
                if exist C:\\TEST\\calculator.py (
                    python C:\\TEST\\calculator.py
                ) else (
                    echo "Fichier calculator.py non trouvé dans le répertoire TEST"
                )
               
                '''
            }
        }
        post{
            always{
                archiveArtifacts artifacts: 'Release.zip', fingerprint: true
            }
            success {
                echo '================================'
                echo 'Déploiement terminé avec succès.'
                echo '================================'
                            }
            failure {
                echo '================================'
                echo 'Déploiement échoué.'
                echo '================================'
            }

        }
    }
}