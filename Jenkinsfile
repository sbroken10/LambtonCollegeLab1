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
                // *** ¡ESTE ES EL INTENTO FINAL USANDO SÓLO LAS SUGERENCIAS DE ERROR Y SIN COMENTARIOS EN LUGAR INCORRECTO! ***
                // Si esto falla, la versión de tu plugin es probablemente demasiado antigua/peculiar
                // y necesitará una actualización o consulta de su documentación específica.

                dependencyCheck(
                    odcInstallation: 'Dependency-Check_Latest', // 'dependencyCheckInstallation' -> 'odcInstallation'
                    
                    project: 'LambtonCollegePythonApp',
                    scanPath: '.',
                    outputPath: 'dependency-check-report/',
                    format: 'HTML,XML,JSON',
                    
                    // Aplicando las sugerencias directas para stopBuild y skipOnScmChange
                    // Esto es una CONJETURA FUERTE sobre cómo el plugin combina estos parámetros.
                    stopBuild: true,
                    skipOnScmChange: false,
                    
                    additionalArguments: '--enableExperimental' 
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
