pipeline{
    agent any
    options {
        skipDefaultCheckout()
    }   
    tools{
        maven "mvn"
        nodejs "node"
    }

    stages{
        stage('Checkout') {
            steps{
                git branch: 'master', credentialsId: 'Git token', url : 'https://github.com/Zed2199/Expense_Tracker.git'
            }
        }

        stage('Build'){
            parallel {
                stage('java') {
                    steps {
                        dir('expense-tracker-service') {
                            sh 'mvn clean install'
                        }
                    }
                }
                
                stage('Angular') {
                    steps {
                        dir('expense-tracker-ui') {
                            sh 'npm install'
                            sh 'npx ng build --configuration production'
                        }
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'cd expense-tracker-service && mvn test'
                }
            }
        }
    }

    post {
        success {
            echo 'Build was successful!'
        }

        failure {
            echo 'Build failed. Check logs'
        }
    }

}