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

        // --- ELIMINA ESTE STAGE SI NO QUIERES EJECUTAR TESTS ---
        // stage('Run Python Tests (Optional)') {
        //     steps {
        //         script {
        //             sh '. .venv/bin/activate && pytest'
        //         }
        //     }
        // }
        // ----------------------------------------------------

        stage('Security Scan: Dependency-Check') {
            steps {
                script {
                    dependencyCheck additionalArguments: '--enableExperimental --logLevel INFO',
                                    autoUpdate: true,
                                    failBuildOnCVSS: 7,
                                    outputPath: 'dependency-check-report/',
                                    project: 'LambtonCollegePythonApp',
                                    scanPath: '.',
                                    reportFormat: 'HTML,XML,JSON',
                                    skipOnError: false

                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'
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
            cleanWs()
        }
    }
}
