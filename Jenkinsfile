pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TU-USUARIO/TU-REPO.git'
            }
        }

        stage('Build (mvnw)') {
            steps {
                bat 'mvnw.cmd -B clean package'
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube-local') {
                    bat """
                    sonar-scanner ^
                      -Dsonar.projectKey=back_app_v2 ^
                      -Dsonar.sources=src/main/java ^
                      -Dsonar.java.binaries=target/classes ^
                      -Dsonar.host.url=http://localhost:9000
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Quality Gate falló: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage('Archive jar') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
