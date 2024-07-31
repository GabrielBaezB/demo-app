pipeline {
    agent any

    tools {
        maven 'Default Maven' // Asegúrate de que este nombre coincida con la configuración en Jenkins
    }

    environment {
        SONAR_TOKEN = credentials('sonarToken') // Asegúrate de que 'sonarToken' es el ID correcto de tus credenciales
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

        stage('Upload Artifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'localhost:8081',
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: 'sonar-test',
                    credentialsId: 'NexusLogin',
                    artifacts: [
                        [artifactId: 'webApp',
                         classifier: '',
                         file: 'web/target/time-tracker-web-0.5.0-SNAPSHOT.war',
                         type: 'war'],
                        [artifactId: 'coreApp',
                         classifier: '',
                         file: 'core/target/time-tracker-core-0.5.0-SNAPSHOT.jar',
                         type: 'jar']
                    ]
                )
            }              
        }
    }
}
