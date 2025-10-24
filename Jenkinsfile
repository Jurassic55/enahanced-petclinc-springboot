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
}
    }
}