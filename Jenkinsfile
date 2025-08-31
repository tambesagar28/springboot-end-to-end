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
                    withCredentials([usernamePassword(credentialsId:'dockerhub-creds', usernameVariable:'D_USER',passwordvariable:'D_PASS')]){
                        sh """
                            echo ${D_PASS} | docker login -u ${D_USER} --password-stdin
                            docker build -t ${IMAGE}:${TAG} .
                            docker push ${IAMGE}:${TAG}
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
                            expose kubeconfig=${KUBECONFIG-FILE}
                            sed s/PLACEHOLDER/${TAG}/g k8s/deployment.yaml > deployment.applied.yaml
                            kubectl apply -f deployment.applied.yaml
                            kubectl apply -f service.yaml
                        """
                    }
                }
            }
        }
    }
}