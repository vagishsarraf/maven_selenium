pipeline {
    agent any
    tools {
        maven "maven"
        docker "docker"
    }

    stages {
    stage('Prepare Selenoid') {
                steps {
                    sh 'docker-compose up -d'
                }
            }
        stage('Sonarqube Analysis - SAST') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=maven-jenkins-pipeline -Dsonar.host.url=http://127.0.0.1:9000"
                }
            }
        }
        stage('Build Artifact') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archive 'target/*.jar'
            }
        }
        stage('Test Maven - JUnit') {
            steps {
                sh "mvn test"
            }
            post{
                always{
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
}