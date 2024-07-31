pipeline {
    agent any

    tools {
        maven 'Default Maven' // Asegúrate de que este nombre coincida con la configuración en Jenkins
    }

    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool name: 'Default Maven', type: 'hudson.tasks.Maven$MavenInstallation'
                    withSonarQubeEnv('Sonar') {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-qube -Dsonar.projectName='con-jenkins' -Dsonar.login=${sonar-qube-key}"
                    }
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'nexus-credentials', passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
                        sh """
                        mvn deploy:deploy-file \
                            -DgroupId=com.mycompany.app \
                            -DartifactId=my-app \
                            -Dversion=1.0-SNAPSHOT \
                            -Dpackaging=jar \
                            -Dfile=target/my-app-1.0-SNAPSHOT.jar \
                            -DrepositoryId=nexus \
                            -Durl=http://localhost:8081/repository/maven-releases/ \
                            -Dusername=${NEXUS_USERNAME} \
                            -Dpassword=${NEXUS_PASSWORD}
                        """
                    }
                }
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
