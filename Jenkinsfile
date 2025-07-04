pipeline {
    agent any

    environment {
        BRANCH_NAME = 'feature-1.1'
    }

    tools {
        maven 'MVN_HOME'
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
    }
}
