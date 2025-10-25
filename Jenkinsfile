pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        IMAGE_NAME = 'springbootapp'
        IMAGE_TAG = 'latest'
        TENANT_ID = '97a60045-4b7e-43fd-b3cb-72bd7eca9e24'
        ACR_NAME = 'springbootdockerreg222'
        ACR_LOGIN_SERVER = 'springbootdockerreg222.azurecr.io'
        FULL_IMAGE_NAME = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        SCANNER_HOME = tool 'sonar-scanner'
        RG          = 'rgdemo'
        NAME = 'myAKSCluster'
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
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=jurassic55 \
                        -Dsonar.projectName=springbootjavaapp \
                        -Dsonar.projectKey=jurassic55_springbootjavaapp \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target
                    '''
                }
            }
        }

        stage('Maven Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Sonar Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    echo "Building Docker Image..."
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Azure Login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn',
                                                 usernameVariable: 'AZURE_USERNAME',
                                                 passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                        echo "Logging in to Azure..."
                        sh """
                            az login --service-principal \
                                -u $AZURE_USERNAME \
                                -p $AZURE_PASSWORD \
                                --tenant $TENANT_ID

                            az acr login --name $ACR_NAME
                        """
                    }
                }
            }
        }

        stage('Docker Push to ACR') {
            steps {
                script {
                    echo "Pushing Docker image to ACR..."
                    sh """
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                        docker push ${FULL_IMAGE_NAME}
                    """
                }
            }
        }
               stage('Azure Login to AKR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn',
                                                 usernameVariable: 'AZURE_USERNAME',
                                                 passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                        echo "Logging in to AKS"
                        sh """
                            az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                            az aks get-credentials --resource group $RG --name $NAME --overwrite-existing
                        """
                    }
    }
}
    stage('Azure Login to AKR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn',
                                                 usernameVariable: 'AZURE_USERNAME',
                                                 passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                        echo "Deploy to AKS Cluster"
                        sh """
                            az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                            kubectl apply -f k8s/sprinboot-deployment.yaml
                        """
                    }
    
                                                 }
            }
