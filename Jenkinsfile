pipeline {
    agent any

    tools {
        // Asegúrate de que 'Dependency-Check_Latest' esté configurado en Manage Jenkins -> Global Tool Configuration.
        'dependency-check' 'Dependency-Check_Latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/sbroken10/LambtonCollegeLab1.git'
            }
        }

        stage('Setup Python Environment and Install Dependencies') {
            steps {
                script {
                    sh 'python3 -m venv .venv' // Create a virtual environment
                    // Activate and install dependencies from requirements.txt
                    sh '. .venv/bin/activate && pip install --no-cache-dir -r requirements.txt'
                }
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                // *** ¡LA SOLUCIÓN FINAL PARA LOS additionalArguments! ***
                // Los argumentos CLI se ajustan para que coincidan con la ayuda de la herramienta.
                dependencyCheck(
                    odcInstallation: 'Dependency-Check_Latest',
                    additionalArguments: '''
                        --project LambtonCollegePythonApp \
                        --scan . \
                        --format HTML JSON XML \
                        --out dependency-check-report/ \
                        --enableExperimental \
                        --failOnCVSS 7 \
                        --data . \
                        --nvdApiKey 92091260-bf0d-4e3e-9acd-68596b26f830
                    '''.stripIndent().trim()
                )

                // Publica los resultados del escaneo en la interfaz de Jenkins.
                dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'

                // Opcional: Archiva el reporte HTML para poder descargarlo fácilmente.
                archiveArtifacts artifacts: 'dependency-check-report/dependency-check-report.html', fingerprint: true
            }
        }

        stage('Post-Build Actions') {
            steps {
                echo "Pipeline finished!"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
