pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'MVN_HOME' 
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git', branch: 'main'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Nexus-credentials', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
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
                          -Dusername=${NEXUS_USER} \
                          -Dpassword=${NEXUS_PASS}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and Deploy Successful!'
        }
        failure {
            echo '❌ Build or Deploy Failed!'
        }
    }
}






