
pipeline {
    agent any // O puedes especificar un 'agent' con una etiqueta (label) específica si tienes nodos configurados

    tools {
        // CORRECCIÓN 1: 'dependencyCheck' debe ser 'dependency-check' para que Jenkins lo reconozca.
        // Asegúrate que 'Dependency-Check_Latest' es el nombre que le diste en Manage Jenkins -> Global Tool Configuration.
        'dependency-check' 'Dependency-Check_Latest'

        // CORRECCIÓN 2: Asegúrate que 'Maven_3.8.8' esté configurado exactamente con ese nombre
        // en Manage Jenkins -> Global Tool Configuration -> Maven installations.
        maven 'Maven_3.8.8'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Cambia esta URL y la rama por las de tu repositorio real
                git branch: 'main', url: 'https://github.com/sbroken10/LambtonCollegeLab1.git'
            }
        }

        stage('Build Project') {
            steps {
                // Este comando asume que es un proyecto Maven.
                // Si es Gradle, usa 'sh './gradlew build -x test''
                // '-DskipTests' para omitir los tests unitarios durante la compilación si los quieres ejecutar en otro stage
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                script {
                    // Ejecuta la herramienta OWASP Dependency-Check.
                    // Los parámetros son cruciales y deben adaptarse a tu proyecto.
                    dependencyCheck additionalArguments: '--enableExperimental --logLevel INFO', // Argumentos adicionales para Dependency-Check
                                    autoUpdate: true, // Deja que Dependency-Check actualice su base de datos de vulnerabilidades
                                    failBuildOnCVSS: 7, // Falla el build si encuentra una vulnerabilidad con CVSS >= 7
                                    outputPath: 'dependency-check-report/', // Directorio donde se guardarán los reportes
                                    project: 'LambtonCollegeLab1', // Nombre que aparecerá en el reporte de Jenkins
                                    scanPath: 'target', // Para proyectos Maven, escanea el directorio 'target' donde están los JARs compilados
                                    reportFormat: 'HTML,XML,JSON', // Genera reportes en HTML (para visualizar), XML (para el plugin Jenkins) y JSON
                                    skipOnError: false // No salta el escaneo si Dependency-Check encuentra un error interno

                    // Publica los resultados del escaneo en la interfaz de Jenkins.
                    // La ruta debe apuntar al archivo XML generado por Dependency-Check.
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'

                    // Opcional: Archiva el reporte HTML para poder descargarlo fácilmente desde el build de Jenkins.
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

