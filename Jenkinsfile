pipeline {
    agent any

    tools {
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
                // *** ¡ESTE ES EL INTENTO FINAL USANDO SÓLO LAS SUGERENCIAS DE ERROR! ***
                // Si esto falla, la versión de tu plugin es probablemente demasiado antigua/peculiar
                // y necesitará una actualización o consulta de su documentación específica.

                dependencyCheck(
                    odcInstallation: 'Dependency-Check_Latest', // 'dependencyCheckInstallation' -> 'odcInstallation'
                    
                    # Las siguientes sugerencias de 'debug' son muy inusuales.
                    # Es posible que el plugin espere un mapa Groovy literal o una cadena JSON para el parámetro 'debug',
                    # y que las otras opciones (project, scanPath, outputPath, format) vayan DENTRO de ese 'debug'.
                    # Dado que no sabemos el formato exacto de 'debug', la única opción es ponerlas como parámetros individuales
                    # con los nombres que te sugirió ANTES del cambio a 'debug' para todos.
                    # Pero el último error sugiere 'debug' para TODOS.

                    # Si 'debug' tiene que encapsular todo, sería algo así (altamente inusual):
                    # debug: [
                    #   project: 'LambtonCollegePythonApp',
                    #   scanPath: '.',
                    #   outputPath: 'dependency-check-report/',
                    #   format: 'HTML,XML,JSON'
                    # ],

                    # Dado lo inusual de 'debug' como reemplazo para todos, y que "stopBuild"
                    # fue sugerido para dos de ellas, intentemos con los parámetros que ya intentamos
                    # pero usando 'odcInstallation' y forzando 'stopBuild' si hay un error:

                    project: 'LambtonCollegePythonApp', # Manteniendo el nombre, aunque se sugirió 'debug'
                    scanPath: '.', # Manteniendo el nombre, aunque se sugirió 'debug'
                    outputPath: 'dependency-check-report/', # Manteniendo el nombre, aunque se sugirió 'debug'
                    format: 'HTML,XML,JSON', # Manteniendo el nombre, aunque se sugirió 'debug'
                    
                    # Aplicando las sugerencias directas para stopBuild y skipOnScmChange
                    stopBuild: true, # Combina failBuildOnCVSS y autoUpdate en uno solo. Esto es un GUESS FUERTE.
                                     # Si esto falla, significa que no combinan, y deberíamos probar
                                     # stopBuild para cada uno por separado: autoUpdate: true, stopBuild: 7 (raro)

                    skipOnScmChange: false, # Reemplazo para 'skipOnError'
                    
                    # additionalArguments es generalmente robusto y se pasa directamente al binario
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
