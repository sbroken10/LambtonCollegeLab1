pipeline {
    agent any // O puedes especificar un 'agent' con una etiqueta (label) específica si tienes nodos configurados

    tools {
        // Asegúrate de que 'Dependency-Check_Latest' esté configurado en Jenkins Global Tool Configuration
        'dependency-check' 'Dependency-Check_Latest'
        // Ya no necesitamos 'maven' aquí
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
                    // Instala Python (si no está disponible o quieres una versión específica)
                    // Este paso puede variar mucho dependiendo de cómo tienes configurado tu agente Jenkins.
                    // A veces, basta con asegurar que 'python3' esté en el PATH.
                    // Para entornos virtuales, primero crea el venv y luego activa.
                    sh 'python3 -m venv .venv' // Crea un entorno virtual
                    sh '. .venv/bin/activate && pip install --no-cache-dir -r requirements.txt' // Activa y instala dependencias
                    // Asegúrate de tener un archivo 'requirements.txt' en la raíz de tu repositorio
                }
            }
        }

        stage('Run Python Tests (Optional)') {
            steps {
                script {
                    // Ejecuta tus tests Python, por ejemplo, con pytest
                    // Asume que pytest está en tu requirements.txt
                    sh '. .venv/bin/activate && pytest'
                }
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                script {
                    // Ejecuta la herramienta OWASP Dependency-Check.
                    // Para Python, Dependency-Check necesita escanear el 'requirements.txt'
                    // o el entorno virtual/librerías instaladas.

                    // El analizador de Python de Dependency-Check puede escanear un requirements.txt
                    // o directorios que contengan paquetes Python instalados.
                    // 'scanPath: '.'' es a menudo una buena opción para que Dependency-Check
                    // intente encontrar el requirements.txt u otros archivos de dependencia.
                    dependencyCheck additionalArguments: '--enableExperimental --logLevel INFO',
                                    autoUpdate: true,
                                    failBuildOnCVSS: 7,
                                    outputPath: 'dependency-check-report/',
                                    project: 'LambtonCollegePythonApp', // Nombre de tu proyecto Python
                                    scanPath: '.', // Escanea el directorio actual del workspace de Jenkins
                                    reportFormat: 'HTML,XML,JSON',
                                    skipOnError: false

                    // Publica los resultados del escaneo en la interfaz de Jenkins.
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'

                    // Opcional: Archiva el reporte HTML para poder descargarlo fácilmente.
                    archiveArtifacts artifacts: 'dependency-check-report/dependency-check-report.html', fingerprint: true
                }
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
