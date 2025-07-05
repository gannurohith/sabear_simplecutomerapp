pipeline {
    agent any

    environment {
        // SonarQube
        SONAR_SCANNER_HOME = tool 'SonarQube'
        SONAR_QUBE_CREDENTIALS_ID = 'TToken1'
        SONAR_QUBE_NAME = 'sonarqube-server'

        // Nexus
        NEXUS_REPOSITORY_ID = 'Nexus_customer_app'
        NEXUS_URL = 'http://3.89.115.90:8081/repository/Nexus_customer_app/'
        NEXUS_CREDENTIALS_ID = 'Nexus-credentials'

        // Tomcat
        TOMCAT_URL = 'http://34.202.205.194:8080/manager/text'
        TOMCAT_CREDENTIALS_ID = 'tomcat-credentials'
        TOMCAT_APP_CONTEXT = 'SimpleCustomerApp'
    }

    tools {
        maven 'MVN_HOME'
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'master', url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: "${SONAR_QUBE_CREDENTIALS_ID}", installationName: "${SONAR_QUBE_NAME}") {
                    sh 'mvn clean verify sonar:sonar -Dsonar.login=$SONAR_TOKEN -DskipTests'
                }
            }
        }

        stage('Maven Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${NEXUS_CREDENTIALS_ID}",
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        mvn deploy:deploy-file \
                          -DgroupId=com.javatpoint \
                          -DartifactId=SimpleCustomerApp \
                          -Dversion=1.0.0-SNAPSHOT \
                          -Dpackaging=war \
                          -Dfile=target/SimpleCustomerApp-1.0.0-SNAPSHOT.war \
                          -DrepositoryId=Nexus_customer_app \
                          -Durl=http://3.89.115.90:8081/repository/Nexus_customer_app/ \
                          -DgeneratePom=true \
                          -DretryFailedDeploymentCount=3 \
                          -Dusername=$NEXUS_USER \
                          -Dpassword=$NEXUS_PASS
                    '''
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = findFiles(glob: 'target/*.war')[0]
                    if (warFile) {
                        sh "mv ${warFile.path} target/${env.TOMCAT_APP_CONTEXT}.war"
                        step([
                            $class: 'DeployPublisher',
                            adapters: [[
                                $class: 'Tomcat9xAdapter',
                                credentialsId: "${TOMCAT_CREDENTIALS_ID}",
                                url: "${TOMCAT_URL}"
                            ]],
                            war: "target/${env.TOMCAT_APP_CONTEXT}.war",
                            contextPath: "${env.TOMCAT_APP_CONTEXT}"
                        ])
                    } else {
                        error 'WAR file not found!'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo '✅ Pipeline succeeded.'
        }
        failure {
            echo '❌ Build or Deployment Failed!'
        }
    }
}

