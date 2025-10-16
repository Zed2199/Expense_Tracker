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
                            sh 'npm run build'
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

        stage('SonarQube Analysis') {
            steps {
                dir('expense-tracker-service') {
                    withSonarQubeEnv('MySonarServer') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }

            post {
                success {
                    script {
                        timeout(time: 2, unit: 'MINUTES') {
                            def qualityGate = waitForQualityGate()
                            if(qualityGate.status != 'OK'){
                              error "SonarQube Quality Gate failed"
                            } else {
                              echo "SonarQube Quality Gate passed"
                            }
                        }
                    }
                }
                failure {
                    echo 'SonarQube analysis failed'
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