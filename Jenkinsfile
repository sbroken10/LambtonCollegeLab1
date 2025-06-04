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

        

        stage('Security Scan: Dependency-Check') {
            steps {
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
                        --data .
                        --nvdDatafeedTimeout 1200000 # Aumentar el tiempo de espera a 20 minutos (en milisegundos)
                    '''.stripIndent().trim()
                    // Si quieres pasar skipOnError, busca su argumento CLI si existe, por ejemplo: --skipOnError
                )

                dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'
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
