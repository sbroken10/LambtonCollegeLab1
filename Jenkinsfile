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
                // *** ¡LA SOLUCIÓN BASADA EN EL SNIPPET GENERATOR! ***
                // Todos los parámetros de configuración de Dependency-Check van dentro de 'additionalArguments'.
                // 'odcInstallation' es el único parámetro de nivel superior.

                dependencyCheck(
                    odcInstallation: 'Dependency-Check_Latest',
                    additionalArguments: '''
                        --project LambtonCollegePythonApp
                        --scan .
                        --format HTML JSON XML
                        --output dependency-check-report/
                        --enableExperimental
                        --failOnCVSS 7
                        --autoUpdate
                        --nvdApiKey YourNvdApiKeyHere # Opcional: Si tienes una clave API de NVD para más actualizaciones
                    '''.stripIndent().trim()
                    // Si quieres pasar skipOnError, busca su argumento CLI si existe, por ejemplo: --skipOnError
                )

                // Publica los resultados del escaneo en la interfaz de Jenkins.
                // Esto DEBE permanecer fuera de additionalArguments, ya que es un paso de Jenkins.
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
