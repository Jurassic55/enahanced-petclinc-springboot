pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
            stage ('Checkout from GIT') {
                steps {
                    git branch: 'prod', url: 'https://github.com/Jurassic55/enahanced-petclinc-springboot.git'

                }
            }
            stage('compile with maven') {
                steps {
                    sh 'mvn validate'    

                }
            }
             stage('validate with maven') {
                steps {
                    sh 'mvn compile'    

                }
                Stage ('sonar analysis') (
                    environment{
                        scanner_Home = tool 'Sonar-Scanner'
                    }
                    steps { 
                        withSonarQubeEnv("sonarserver") {
                            ssh'''$(SCANNER_HOME)/bin/sonar-scanner \
                             -Dsonar.organisation=Jurassic55 \
                             -Dsonar.projectName=springbootjavaapp
                             -Dsonar.projectKey=jurassic55_springbootjavaapp
                             -Dsonar.java.binaries=. \
        
                             '''


                        }

                    }
                )
}
    }
}