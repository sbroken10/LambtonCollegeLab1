pipeline {
    agent any // O puedes especificar un 'agent' con una etiqueta (label) específica si tienes nodos configurados

    tools {
        // Asegúrate de que 'Dependency-Check_Latest' esté configurado en Manage Jenkins -> Global Tool Configuration.
        'dependency-check' 'Dependency-Check_Latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Cambia esta URL y la rama por las de tu repositorio real de Python
                git branch: 'main', url: 'https://github.com/sbroken10/LambtonCollegeLab1.git'
            }
        }

        stage('Setup Python Environment and Install Dependencies') {
            steps {
                script {
                    sh 'python3 -m venv .venv' // Crea un entorno virtual
                    // Activa y instala dependencias de requirements.txt
                    sh '. .venv/bin/activate && pip install --no-cache-dir -r requirements.txt'
                }
            }
        }

        stage('Run Python Tests (Optional)') {
            steps {
                script {
                    // Activa el entorno virtual y ejecuta pytest
                    sh '. .venv/bin/activate && pytest'
                }
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                // *** ¡LA CORRECCIÓN CLAVE ESTÁ AQUÍ! ***
                // Usamos el nombre del paso "dependencyCheck" con minúscula inicial
                // y los parámetros comunes que se usan para este plugin.
                // Si la interfaz no te los muestra, confiamos en la documentación estándar
                // y en que la instalación del plugin está correcta.

                dependencyCheck(
                    dependencyCheckInstallation: 'Dependency-Check_Latest', // El nombre del parámetro que aparece en tu captura de pantalla
                    project: 'LambtonCollegePythonApp',
                    scanPath: '.', // Escanea el directorio actual del workspace
                    outputPath: 'dependency-check-report/', // Dónde guardar los reportes
                    format: 'HTML,XML,JSON', // Formatos de reporte
                    failBuildOnCVSS: 7, // Fallar si la severidad CVSS es 7 o más
                    autoUpdate: true, // Actualizar la base de datos de vulnerabilidades
                    additionalArguments: '--enableExperimental', // Argumentos adicionales (opcional)
                    // La línea problemática del comentario ha sido eliminada.
                    // Si el parámetro 'skipOnError' realmente es necesario y te da error,
                    // tendremos que buscar el nombre correcto en la documentación de tu plugin.
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
            // Limpia el workspace de Jenkins después de cada ejecución del pipeline para ahorrar espacio.
            cleanWs()
        }
    }
}

