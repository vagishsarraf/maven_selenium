pipeline {
    agent any
    tools {
        maven "maven"
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
                    sh "mvn sonar:sonar -Dsonar.projectKey=maven-jenkins-pipeline"
                }
                script {
                    def qualitygate = waitForQualityGate()
                    if (qualitygate.status != "OK") {
                        error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
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
    post{
        always {
            sh 'docker-compose down'
        }
    }
}