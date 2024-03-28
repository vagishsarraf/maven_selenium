pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven"
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/vagishsarraf/maven_selenium.git'

                // Run Maven on a Unix agent.
                sh 'ls'
                sh "mvn verify sonar:sonar -Dsonar.projectKey=maven_CICD -Dsonar.projectName='maven_CICD' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=maven"
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}