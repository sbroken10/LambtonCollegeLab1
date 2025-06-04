pipeline {
    agent any // O puedes especificar un 'agent' con una etiqueta (label) específica si tienes nodos configurados

    tools {
        // Asegúrate de que 'Dependency-Check_Latest' es el nombre que le diste en Manage Jenkins -> Global Tool Configuration.
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
                    sh 'python3 -m venv .venv'
                    sh '. .venv/bin/activate && pip install --no-cache-dir -r requirements.txt'
                }
            }
        }

        stage('Run Python Tests (Optional)') {
            steps {
                script {
                    sh '. .venv/bin/activate && pytest'
                }
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                // Aquí está la CORRECCIÓN clave
                // Usamos el paso 'dependencyCheck' (CamelCase) y le pasamos el nombre de la instalación.
                // Los parámetros se pasan como argumentos al paso.
                dependencyCheck installation: 'Dependency-Check_Latest', // <--- Importante: especifica la instalación aquí
                                additionalArguments: '--enableExperimental --logLevel INFO',
                                autoUpdate: true,
                                failBuildOnCVSS: 7,
                                outputPath: 'dependency-check-report/',
                                project: 'LambtonCollegePythonApp',
                                scanPath: '.',
                                reportFormat: 'HTML,XML,JSON',
                                skipOnError: false

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
