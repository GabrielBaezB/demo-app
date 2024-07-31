pipeline {
    agent any

    tools {
        maven 'Default Maven' // Asegúrate de que este nombre coincida con la configuración en Jenkins
    }

    environment {
        SONAR_TOKEN = credentials('sonarToken') // Reemplaza 'sonar-token-id' con el ID de tus credenciales
    }

    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool name: 'Default Maven', type: 'hudson.tasks.Maven$MavenInstallation'
                    withSonarQubeEnv('Sonar') { 
                        sh "${mvn}/bin/mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
            }
        }

        stage('Upload Artifact') {  // Corregido: comillas curvas a rectas
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',  // Corregido: comillas curvas a rectas
                    protocol: 'http',  // Corregido: comillas curvas a rectas
                    nexusUrl: 'localhost:8081',  // Corregido: comillas curvas a rectas
                    groupId: 'QA',  // Corregido: comillas curvas a rectas
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: 'sonar-test',  // Corregido: comillas curvas a rectas
                    credentialsId: 'NexusLogin',  // Corregido: comillas curvas a rectas
                    artifacts: [
                        [artifactId: 'webApp',  // Corregido: comillas curvas a rectas
                        classifier: '',
                        file: 'web/target/time-tracker-web-0.5.0-SNAPSHOT.war',
                        type: 'war'],  // Corregido: comillas curvas a rectas
                        [artifactId: 'coreApp',  // Corregido: comillas curvas a rectas
                        classifier: '',
                        file: 'core/target/time-tracker-core-0.5.0-SNAPSHOT.jar',
                        type: 'jar']  // Corregido: comillas curvas a rectas
                    ]
                )
            }              
        }

        stage('Build') {
            steps {
                echo 'Building...'
                mvn clean package
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                // Aquí puedes agregar tus comandos de prueba, por ejemplo:
                // sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Aquí puedes agregar tus comandos de despliegue, por ejemplo:
                // sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
