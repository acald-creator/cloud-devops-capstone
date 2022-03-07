pipeline {
    agents any
    stages {
        stage('Connect local Kubernetes cluster to AWS EKS') {
            steps {
                withAWS(region:'us-east-1', credentials:'aws-static') {
                    sh '''
                        aws eks --region us-east-1 update-kubeconfig --name hilarious-monster-1646618989
                        '''
                }
            }
        } 
    }
}