pipeline {
    agent any

    environment {
        // SonarQube
        SONARQUBE_ENV = 'sonarqube-server'
        SONAR_TOKEN_ID = 'TToken1'

        // Nexus
        NEXUS_REPO_ID = 'Nexus_customer_app'
        NEXUS_URL = 'http://3.89.115.90:8081/repository/Nexus_customer_app/'

        // Tomcat
        TOMCAT_URL = 'http://34.202.205.194:8080/manager/text'
        TOMCAT_CREDENTIALS_ID = 'tomcat-credentials'
        TOMCAT_APP_CONTEXT = 'simplecustomerapp'
    }

    tools {
        maven 'MVN_HOME'
    }

    stages {
        stage('Clone Code') {
            steps {
                git branch: 'master', url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    withCredentials([string(credentialsId: "${SONAR_TOKEN_ID}", variable: 'SONAR_TOKEN')]) {
                        sh '''
                            mvn clean verify sonar:sonar \
                              -Dsonar.login=$SONAR_TOKEN \
                              -DskipTests
                        '''
                    }
                }
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Nexus') {
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
                        // Rename WAR to context
                        sh "mv ${warFile.path} target/${env.TOMCAT_APP_CONTEXT}.war"
                        step([
                            $class: 'DeployPublisher',
                            adapters: [[
                                $class: 'Tomcat9xAdapter',
                                credentialsId: "${env.TOMCAT_CREDENTIALS_ID}",
                                url: "${env.TOMCAT_URL}"
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
            echo "Pipeline execution completed with status: ${currentBuild.currentResult}"
        }
        success {
            echo '✅ Build and Deployment Successful!'
        }
        failure {
            echo '❌ Build or Deployment Failed!'
        }
    }
}
