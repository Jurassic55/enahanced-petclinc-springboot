pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment{
        IMAGE_NAME='springbootapp'
        IMAGE_TAG='latest'
        TENANT_ID='97a60045-4b7e-43fd-b3cb-72bd7eca9e24'
        ACR_NAME='springbootdockerreg222'
        ACR_LOGIN_SERVER='springbootdockerreg222.azurecr.io'
        FULL_IMAGE_NAME='$(ACR_LOGIN_SERVER)/$IMAGE_NAME:$IMAGE_TAG'

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
                waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
            }
        }
    }
    stage('Docker Build') {
    steps {
        script {
            echo "Building Docker Image...."
            docker.build("$(IMAGE_NAME):$(IMAGE_TAG)")
        }
        
    }
   
            }
    stage('Azure Login TO ACR')    
        steps{
            withCredentials([usernamePassword(credentialsId: 'azure-acr-spn', usernameVariable: 'AZURE_USERNAME',passwordVariable: 'AZURE_PASSWORD')])
            script {
                echo "Azure Login Started"
                sh '''
                az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --TENANT_ID  
                az acr login --name $ACR_NAME
                '''
                }

            }
        }
        stage('Docker push to ACR') {
            steps{
                script {
                    echo "Docker Image to ACR"
                    sh '''
                    docker.tag $(IMAGE_NAME):$(IMAGE_TAG):$(FULL_IMAGE_NAME)
                    docker push $(FULL_IMAGE_NAME)
                    '''


                }
            }
        }
    }
}
    

