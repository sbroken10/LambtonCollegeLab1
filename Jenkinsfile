pipeline {
    agent any // Or specify a 'label' for your Jenkins agent

    tools {
        // Ensure 'Dependency-Check_Latest' is configured in Manage Jenkins -> Global Tool Configuration.
        // The name 'dependency-check' here matches the tool type expected by Jenkins.
        'dependency-check' 'Dependency-Check_Latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Change this URL and branch to your actual Python repository
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

        stage('Run Python Tests (Optional)') {
            steps {
                script {
                    // Activate the virtual environment and run pytest
                    sh '. .venv/bin/activate && pytest'
                }
            }
        }

        stage('Security Scan: Dependency-Check') {
            steps {
                // This syntax is adjusted based on the "did you mean" suggestions from your previous Jenkins error log.
                // 'odcInstallation' is the corrected parameter name for specifying the tool installation.
                // 'format' is used instead of 'reportFormat' based on common plugin usage.
                dependencyCheck odcInstallation: 'Dependency-Check_Latest',
                                autoUpdate: true,
                                failBuildOnCVSS: 7,
                                project: 'LambtonCollegePythonApp',
                                scanPath: '.',
                                format: 'HTML,XML,JSON',
                                outputPath: 'dependency-check-report/',
                                skipOnError: false // Manteniendo este parámetro. Si vuelve a fallar por "skipOnError", tendremos que revisar de nuevo el Snippet Generator para ese parámetro específico.

                // Publish the Dependency-Check results for Jenkins UI
                dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.xml'

                // Archive the HTML report for easy access
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
            // Clean up the Jenkins workspace after every build
            cleanWs()
        }
    }
}
