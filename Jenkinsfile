stages {
        stage('Checkout') {
            steps {
                
                git branch: 'main', url: 'https://github.com/tu-usuario/tu-repositorio.git'
                echo 'Código fuente descargado exitosamente.'
            }
        }

        stage('Build') {
            steps {
               
                script {
                    echo 'Iniciando la etapa de construcción...'
                    echo 'Aplicación "construida" (o proceso de construcción simulado).'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo 'Iniciando la etapa de pruebas...'
                   
                    echo 'Pruebas "ejecutadas" (o proceso de prueba simulado).'
                }
            }
        }
    }

   
    post {
        
        always {
            echo 'La pipeline ha finalizado.'
        }
        
        success {
            echo '¡Pipeline completada exitosamente! ✅'
        }

        failure {
            echo '¡Pipeline fallida! ❌'
        }
    }
