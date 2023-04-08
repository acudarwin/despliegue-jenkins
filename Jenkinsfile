pipeline {
    agent {label 'Node1'}
    environment{
        scannerHome = tool 'sonarqube'
    }
    stages {
            stage('Docker Builds') {
               steps {
                   dir('C:\\jenkins\\lab-4\\backend'){
                       bat 'docker build -t backend .'
                   }
                   dir('C:\\jenkins\\lab-4\\frontend'){
                       bat 'docker build -t frontend .'
                   }
                   bat 'docker images'
                }
                   
            }
            stage('DEV') {
                steps{
                    dir('C:\\jenkins\\lab-4'){
                        bat 'docker compose up -d'
                        bat 'docker compose ps'
                    }
                }
            }
            stage('Save Dockers') {
                steps{
                    dir('C:\\jenkins\\lab-4'){
                        bat "docker build -t http://192.168.X.XX:8082/lab-4:${BUILD_NUMBER} ."
                        bat "docker push http://192.168.X.XX:8082/lab-4:${BUILD_NUMBER}"
                    }
                }
            }
            stage('QA') {
                steps{
                    bat 'curl -v http://192.168.X.XX:8000/'
                    bat 'curl -v http://192.168.X.XX:8084/' 
                }
            }
            stage('PROD') {
                steps{
                    dir('C:\\jenkins\\lab-4'){
                        bat 'docker compose -f docker-compose.yml down'
                        bat 'docker load -i frontend1.0.tar'
                        bat 'docker load -i backend1.0.tar'
                        bat 'docker compose -f docker-compose.yml up -d'
                    }
                }
            }
            stage('Anchore Grype Vulnerability Scanner') {
                steps {
                    bat 'docker run -it --rm anchore/grype:latest acudarwin/backend:latest'
                    bat 'docker run -it --rm anchore/grype:latest acudarwin/frontend:latest'
                }
            }
            stage('Clone Project github') {
                steps {
                    bat 'git clone https://github.com/acudarwin/backend-frontend.git'
                }
            }
            stage('SonarQube Static Code Analysis') {
                steps {
                    script {
                        withSonarQubeEnv('sonarqube') {
                            bat "${scannerHome}\\bin\\sonar-scanner.bat " +
                                "-D sonar.login=admin " +
                                "-D sonar.password=acudarwin " +
                                "-D sonar.projectKey=lab-4 " +
                                "-D sonar.exclusions=vendor/**,resources/**,**/*.java " +
                                "-D sonar.host.url=http://localhost:9000"
                        }
                    }
                }
            }
        }
}