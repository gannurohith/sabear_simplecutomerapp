pipeline {
    agent any

    environment {
        BRANCH_NAME = 'feature-1.1'
    }

    tools {
        maven 'MVN_HOME'
        // SonarQube CLI tool is not explicitly needed here
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: "${env.BRANCH_NAME}",
                    url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Nexus Upload') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus_server', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy \
                          -DskipTests \
                          -Dnexus.username=$NEXUS_USER \
                          -Dnexus.password=$NEXUS_PASS
                    """
                }
            }
        }
    }
}
