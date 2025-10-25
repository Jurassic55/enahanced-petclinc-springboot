pipeline {
    agent any
    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout from GIT') {
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
                sh 'mvn compile'
            }
        }

        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=jurassic55 \
                        -Dsonar.projectName=springbootjavaapp \
                        -Dsonar.projectKey=jurassic55_springbootjavaapp \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target'''
                }
            }
        }
    stage ('maven package') {
        steps {
            sh 'mvn package'
        }
    }
    stage('Sonar Quality Gate') {
        steps {
            timeout(time: 1,unit: 'MINUTES') {
                waitForQualityGate abortpipeline: true, credentialsID: 'sonar'
            }
        }
    }
    stage('Docker Build') {
        steps {
            script {
                echo "Building Docker Image....."
                docker.build ("${IMAGE_NAME}:{IMAGE_TAG}")
            }
        }
    }
    }
}
