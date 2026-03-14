pipeline {
    agent any

    stages {
        stage('Configure') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/Rahul786007/Word-counter']]
                )
            }
        }

        stage('Building image') {
            steps {
                sh 'docker build -t word-counter .'
            }
        }

        stage('Pushing to ECR') {
            steps {
                withAWS(credentials: 'AWS-CRED', region: 'ap-south-1') {

                    sh '''
                    aws ecr get-login-password --region ap-south-1 \
                    | docker login --username AWS \
                    --password-stdin 781978598169.dkr.ecr.ap-south-1.amazonaws.com
                    '''

                    sh '''
                    docker tag word-counter:latest \
                    781978598169.dkr.ecr.ap-south-1.amazonaws.com/jenkins-eks-app:latest
                    '''

                    sh '''
                    docker push \
                    781978598169.dkr.ecr.ap-south-1.amazonaws.com/jenkins-eks-app:latest
                    '''
                }
            }
        }

        stage('K8S Deploy') {
            steps {
                withAWS(credentials: 'AWS-CRED', region: 'ap-south-1') {

                    sh '''
                    aws eks update-kubeconfig \
                    --name jenkins-eks-cluster \
                    --region ap-south-1
                    '''

                    sh 'kubectl apply -f EKS-Deployment.yaml'
                }
            }
        }

        stage('Get Service URL') {
    steps {
        script {
            withAWS(credentials: 'AWS-CRED', region: 'ap-south-1') {
                def serviceUrl = ""
                timeout(time: 5, unit: 'MINUTES') {
                    while(serviceUrl == "") {
                        serviceUrl = sh(
                            script: "kubectl get svc word-counter-service -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'",
                            returnStdout: true
                        ).trim()

                        if(serviceUrl == "") {
                            echo "Waiting for LoadBalancer..."
                            sleep 10
                        }
                    }
                }
                echo "Service URL: http://${serviceUrl}"
            }
        }
    }
}
}
}
