pipeline {
    agent any

    tools {
        maven 'Maven' // Asegúrate de que este nombre coincida con la configuración en Jenkins
    }

    environment {
        // Utiliza el ID de las credenciales configuradas en Jenkins para SonarQube
        SONAR_TOKEN = credentials('sonar-qube-key') // Reemplaza 'sonar-token-id' con el ID de tus credenciales
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
