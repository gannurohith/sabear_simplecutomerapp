pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
    }

    environment {
        SONARQUBE_ENV = 'sonarqube-server'
        ARTIFACT_VERSION = "${BUILD_NUMBER}-SNAPSHOT"
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

        stage('Maven Compile') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Nexus-cred',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    script {
                        def version = "${BUILD_NUMBER}-SNAPSHOT"
                        def artifactFile = "target/SimpleCustomerApp-${version}.war"

                        sh """
                            mvn deploy:deploy-file \
                              -DgroupId=com.javatpoint \
                              -DartifactId=SimpleCustomerApp \
                              -Dversion=${version} \
                              -Dpackaging=war \
                              -Dfile=${artifactFile} \
                              -DrepositoryId=Nexus_customer_app \
                              -Durl=http://54.209.170.7:8081/repository/Nexus_customer_app/ \
                              -DgeneratePom=true \
                              -DretryFailedDeploymentCount=3 \
                              -Dusername=$NEXUS_USER \
                              -Dpassword=$NEXUS_PASS
                        """
                    }
                }
            }
        }
    }
}
