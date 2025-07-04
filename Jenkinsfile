pipeline {
    agent any

    tools {
        maven 'MVN_HOME' // Your configured Maven tool
    }

    environment {
        SONARQUBE_ENV = 'sonarqube-server' // Your SonarQube Jenkins config name
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'feature-1.1', url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    withCredentials([string(credentialsId: 'TToken', variable: 'SONAR_TOKEN')]) {
                        sh '''
                            mvn clean verify sonar:sonar \
                              -Dsonar.login=$SONAR_TOKEN \
                              -DskipTests
                        '''
                    }
                }
            }
        }
    }
}
