pipeline {
    agent any

    environment {
        BRANCH_NAME = 'feature-1.1'
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: "${env.BRANCH_NAME}",
                    url: 'https://github.com/gannurohith/sabear_simplecutomerapp.git'
            }
        }
    }
}

