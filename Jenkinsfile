pipeline {
    agent any // O puedes especificar un 'agent' con una etiqueta (label) específica si tienes nodos configurados

    tools {
        // Asegúrate de que el nombre aquí coincida con el que configuraste en "Global Tool Configuration"
        dependencyCheck 'Dependency-Check_Latest'
        maven 'Maven_3.8.8' // Ejemplo: Si usas Maven, asegúrate de tenerlo configurado en Global Tool Configuration
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/tu-usuario/tu-repositorio.git' // Cambia esto por tu repo
            }
        }

        stage('Build Project') {
            steps {
                // Si es un proyecto Maven:
                sh 'mvn clean install -DskipTests'
                // O si es un proyecto Gradle:
                // sh './gradlew build -x test'
                // Adapta esto a tu comando de compilación
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                script {
                    // Configura los parámetros de Dependency-Check según tus necesidades
                    // Para más opciones, consulta la documentación del plugin de Jenkins
                    // https://plugins.jenkins.io/dependency-check-plugin/
                    def dcHome = tool 'Dependency-Check_Latest' // Obtiene la ruta de instalación de Dependency-Check

                    // Ejecución de Dependency-Check. Los parámetros son cruciales.
                    // project: Nombre que aparecerá en el reporte.
                    // scanPath: Ruta a los archivos o directorios a escanear. Puede ser el directorio del proyecto.
                    // reportFormat: Formato del reporte (HTML, XML, JSON, CSV).
                    // outputDirectory: Donde se guardarán los reportes.
                    // skipOnScmChange: Para no escanear si no hay cambios (útil en pipelines grandes).
                    // failBuildOnCVSS: Falla el build si el puntaje CVSS supera un umbral.
                    // includeGav: Incluye información GAV (Group, Artifact, Version) en los reportes (solo para Maven/Gradle).
                    // suppressionsFile: Ruta a un archivo XML con supresiones de falsos positivos o vulnerabilidades no relevantes.

                    dependencyCheck additionalArguments: '--enableExperimental --logLevel FINE',
                                    autoUpdate: true, // Deja que Dependency-Check actualice su base de datos
                                    failBuildOnCVSS: 7, // Falla si encuentra una vulnerabilidad con CVSS >= 7
                                    outputPath: 'dependency-check-report/', // Directorio donde se generará el reporte
                                    project: 'MiAplicacionJava', // Nombre del proyecto en el reporte
                                    scanPath: 'target', // Para proyectos Maven/Gradle, escanear el directorio 'target' o 'build/libs'
                                    reportFormat: 'HTML,XML,JSON',
                                    skipOnError: false // No salta si hay un error en Dependency-Check
                                    // suppressionsFile: 'path/to/your/suppressions.xml' // Opcional: si tienes un archivo de supresiones

                    // Para publicar los resultados en la interfaz de Jenkins
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml' // Ruta al reporte XML

                    // Opcional: Si quieres ver el reporte HTML directamente
                    // archiveArtifacts artifacts: 'dependency-check-report/dependency-check-report.html', fingerprint: true
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
            // Limpia el workspace después de cada build
            cleanWs()
        }
    }
}
