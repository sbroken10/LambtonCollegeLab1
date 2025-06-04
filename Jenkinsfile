pipeline {
    // 'agent any' significa que Jenkins puede usar cualquier agente (nodo esclavo o el propio master)
    // que esté disponible para ejecutar esta pipeline.
    agent any

    // 'stages' define la secuencia de etapas que componen tu pipeline.
    stages {
        stage('Checkout') { // Etapa para descargar el código
            steps {
                // El comando 'git' clona o actualiza el repositorio especificado.
                // ¡¡¡ASEGÚRATE DE CAMBIAR ESTA URL POR LA DE TU REPOSITORIO REAL!!!
                // Debería ser: 'https://github.com/sbroken10/LambtonCollegeLab1.git'
                git branch: 'main', url: 'https://github.com/sbroken10/LambtonCollegeLab1.git' // CAMBIA ESTA URL
                echo 'Código fuente descargado exitosamente.'
            }
        }

        stage('Build') { // Etapa para "construir" la aplicación
            steps {
                script {
                    echo 'Iniciando la etapa de construcción...'
                    // Aquí irían los comandos reales de construcción, por ejemplo:
                    // sh 'mvn clean package' // Para un proyecto Maven
                    // sh 'npm install && npm run build' // Para un proyecto Node.js
                    echo 'Aplicación "construida" (o proceso de construcción simulado).'
                }
            }
        }

        stage('Test') { // Etapa para ejecutar pruebas (opcional para empezar)
            steps {
                script {
                    echo 'Iniciando la etapa de pruebas...'
                    // Aquí irían los comandos reales de prueba, por ejemplo:
                    // sh 'mvn test' // Para un proyecto Maven
                    // sh 'npm test' // Para un proyecto Node.js
                    echo 'Pruebas "ejecutadas" (o proceso de prueba simulado).'
                }
            }
        }
    } // Cierre del bloque stages

    // 'post' define acciones que se ejecutarán después de que todas las etapas se completen.
    post {
        always { // Se ejecuta siempre, sin importar si la pipeline tuvo éxito o falló.
            echo 'La pipeline ha finalizado.'
        }
        success { // Se ejecuta solo si todas las etapas fueron exitosas.
            echo '¡Pipeline completada exitosamente! ✅'
        }
        failure { // Se ejecuta si alguna etapa falló.
            echo '¡Pipeline fallida! ❌'
        }
    } // Cierre del bloque post
} // Cierre del bloque pipeline
