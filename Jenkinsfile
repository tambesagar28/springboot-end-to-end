pipeline{
    agent any
    environment{
        IMAGE = 'tambesagar28/spring-boot'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages{

        stage('Checkout'){
            steps{
                checkout scm
                }
        }

        stage('Image Build and Push'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId:'dockerhub-creds', usernameVariable:'D_USER',passwordVariable:'D_PASS')]){
                        sh """
                            echo ${D_PASS} | docker login -u ${D_USER} --password-stdin
                            docker build -t ${IMAGE}:${TAG} .
                            docker push ${IMAGE}:${TAG}
                        """
                    }
                }
            }
        }

        stage('K8s Deployment'){
            steps{
                script{
                    withCredentials([file(credentialsId:'kubeconfig', variable:'KUBECONFIG_FILE')]){
                        sh """
                            export KUBECONFIG=${KUBECONFIG_FILE}
                            sed s/PLACEHOLDER/${TAG}/g k8s/deployment.yaml > k8s/deployment.applied.yaml
                            kubectl apply -f k8s/deployment.applied.yaml
                            kubectl apply -f k8s/service.yaml
                        """
                    }
                }
            }
        }
    }

    post{
        always{
            cleanWs()
        }
    }
}