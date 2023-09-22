pipeline {
    agent any
    tools {
        jdk 'jdk-17.0.6+10'
    }
    stages {
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/MrPancetita/memorizerSIN'
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            def mvn = tool 'Default Maven';
            withSonarQubeEnv() {
                sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=MemorizerSIN -Dsonar.projectName='MemorizerSIN'"
            }
        }
        stage('Build') {
            steps {
                sh 'chmod 744 ./mvnw'
                sh './mvnw clean package'
            }
            post {
                always {
                    sh 'echo DUMMY - TODO: Desplegando a staging...'
                }
                success {
                    sh 'echo creando peticion de cambio'
                    jiraSendDeploymentInfo (
                            environmentId: 'en-prod-1',
                            environmentName: 'en-prod-1',
                            environmentType: 'production',
                            serviceIds: [
                                    'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlL2Y3ZmE1NDJhLTgwYWEtNDA3Zi1iMTY1LTU1ZDBiOTdjNTA1NS8yNTg4MTUxZS1hNjBjLTExZWQtYTk1Yi0xMjhiNDI4MTk0MjQ='
                            ],
                            site: 'serconlo.atlassian.net',
                            state: 'in_progress'
                    )
                }
            }
        }
        stage('Despliegue Prod') {
            steps {
                input message: 'Please confirm the status of the change associated to this build number. Click Approved only if the change is already approved', ok: 'Approved'
            }
            post {
                success {
                    sh 'echo DUMMY: Desplegando en produccion...'
                    jiraSendDeploymentInfo (
                            environmentId: 'en-prod-1',
                            environmentName: 'en-prod-1',
                            environmentType: 'production',
                            serviceIds: [
                                    'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlL2Y3ZmE1NDJhLTgwYWEtNDA3Zi1iMTY1LTU1ZDBiOTdjNTA1NS8yNTg4MTUxZS1hNjBjLTExZWQtYTk1Yi0xMjhiNDI4MTk0MjQ='
                            ],
                            site: 'serconlo.atlassian.net',
                            state: 'successful'
                    )
                }
            }
        }
    }
    post {
        always {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.war'
        }
    }
}
