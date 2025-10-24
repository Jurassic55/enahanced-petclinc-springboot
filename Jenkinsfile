pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout from GitHub') {
            steps {
                git branch: 'prod', url: 'https://github.com/Jurassic55/enahanced-petclinc-springboot.git'
            }
        }

        stage('Validate with Maven') {
            steps {
                sh 'mvn validate'
            }
        }

        stage('Compile with Maven') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                            -Dsonar.organization=Jurassic55 \
                            -Dsonar.projectName=springbootjavaapp \
                            -Dsonar.projectKey=jurassic55_springbootjavaapp \
                            -Dsonar.sources=. \
                            -Dsonar.java.binaries=target
                    '''
                }
            }
        }
    }
}
