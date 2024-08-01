pipeline {
    agent any

    tools {
        maven 'Maven' // Asegúrate de que este nombre coincida con la configuración en Jenkins
    }

    environment {
        SONAR_TOKEN = credentials('sonar-qube-key') // Reemplaza 'sonar-qube-key' con el ID de tus credenciales
        BUILD_TIMESTAMP = new Date().format("yyyyMMdd-HHmmss")
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
                    def mvn = tool name: 'Maven', type: 'hudson.tasks.Maven$MavenInstallation'
                    withSonarQubeEnv('SonarQube') {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-qube -Dsonar.projectName='con-jenkins' -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
            }
        }

        stage('Upload Artifact') {
            steps {
                script {
                    try {
                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: 'http://192.168.173.189:8081', // Asegúrate de que esta URL sea correcta
                            groupId: 'QA',
                            version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                            repository: 'nexus-test',
                            credentialsId: 'NexusLogin',
                            artifacts: [
                                [artifactId: 'webApp',
                                classifier: '',
                                file: 'target/demo-app.war',
                                type: 'war']
                            ]
                        )
                    } catch (Exception e) {
                        echo "Error uploading artifact: ${e.message}"
                        throw e
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean package'
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
