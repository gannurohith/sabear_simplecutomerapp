pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
    }

    environment {
        SONARQUBE_ENV = 'sonarqube-server'
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
                    withCredentials([string(credentialsId: 'TToken1', variable: 'SONAR_TOKEN')]) {
                        sh '''
                            mvn clean verify sonar:sonar \
                              -Dsonar.login=$SONAR_TOKEN \
                              -DskipTests
                        '''
                    }
                }
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
    }
}
