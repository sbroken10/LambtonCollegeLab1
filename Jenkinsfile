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
                // IMPORTANT: This syntax is adjusted based on the "did you mean" suggestions
                // from your previous Jenkins error log.
                // 'odcInstallation' is the corrected parameter name for specifying the tool installation.
                // The other parameters are set to their default/common values or
                // to match the suggested names, even if they seem unusual (e.g., 'stopBuild' for autoUpdate).
                dependencyCheck odcInstallation: 'Dependency-Check_Latest',
                                autoUpdate: true,      // Using 'autoUpdate' even if suggested 'stopBuild'
                                failBuildOnCVSS: 7,    // Using 'failBuildOnCVSS' even if suggested 'stopBuild'
                                project: 'LambtonCollegePythonApp', // Project name for the report
                                scanPath: '.',                     // Scan the current workspace directory
                                format: 'HTML,XML,JSON',           // Corrected parameter name if 'reportFormat' fails
                                outputPath: 'dependency-check-report/', // Output directory for reports
                                # The 'skipOnError' parameter might need to be 'skipOnScmChange' or a different name.
                                # If the error persists, you might need to remove 'skipOnError: false'
                                # or check the Pipeline Syntax Generator again for this specific parameter.
                                # Based on the error, 'debug' was suggested for several. This might mean
                                # some parameters are nested or not directly exposed at the top level in your plugin version.
                                # For simplicity, I'm keeping direct parameters as they are commonly used,
                                # but be aware of the "did you mean" suggestion.
                                # If the error persists, you'll need to use the Pipeline Syntax Generator.
                                skipOnError: false

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
