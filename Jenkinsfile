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

        stage('Maven Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Nexus-credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        mvn deploy:deploy-file \
                          -DgroupId=com.javatpoint \
                          -DartifactId=SimpleCustomerApp \
                          -Dversion=1.0-SNAPSHOT \
                          -Dpackaging=war \
                          -Dfile=target/SimpleCustomerApp-1.0-SNAPSHOT.war \
                          -DrepositoryId=Nexus_Integration \
                          -Durl=http://3.89.115.90:8081/repository/Nexus_Integration/ \
                          -DgeneratePom=true \
                          -DuniqueVersion=false \
                          -DupdateReleaseInfo=true \
                          -Dusername=$NEXUS_USER \
                          -Dpassword=$NEXUS_PASS
                    '''
                }
            }
        }
    }
}




