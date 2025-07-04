pipeline {
    agent any

    tools {
        maven 'MVN_HOME'              // Your Maven tool name
        jdk 'jdk21'                // Updated JDK 21 name
        sonarQubeScanner 'sonar-scanner' // Your SonarQube scanner name
    }

    environment {
        SONARQUBE_SERVER = 'sonarqube-server' // Your Jenkins SonarQube server name
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'feature-1.1',
                    url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Publish to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Nexus-cred', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy \
                        -DaltDeploymentRepository=nexus::default::http://<your-nexus-ip>:8081/repository/Nexus_customer_app/ \
                        -Dnexus.username=$NEXUS_USER \
                        -Dnexus.password=$NEXUS_PASS
                    """
                }
            }
        }
    }
}
