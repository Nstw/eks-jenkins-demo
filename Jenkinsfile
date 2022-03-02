
pipeline{

	environment {
		registry = "naomesp/eks-jenkins-demo"
		registryCredential = 'dockerhub-naome'
		dockerImage = ''
		region = "ap-southeast-1"
		clusterName  = "suthawan-labs"
		eksProfile = 'aws-naome'
	}

	agent any
	
	stages {

		stage('Build') {
			steps {
				script {
						dockerImage = docker.build registry + ":$BUILD_NUMBER"
				}   
			}
		}
  

		stage('Push image') {
			steps {
				script {
						docker.withRegistry( '', registryCredential ) {
						dockerImage.push()
					}
				}
			}
		}

        
		stage('Remove Unused docker image') {
			steps{
				sh "docker rmi $registry:$BUILD_NUMBER"
			}
		}

		stage('aws credentials'){
			steps {
				withAWS(credentials: '$eksProfile', region: 'ap-southeast-1') {
				  sh "aws iam list-account-aliases"
				  sh "aws eks --region $region update-kubeconfig --name $clusterName"
			  }
			}
		}

		stage('eks deploy') {
			steps {
				sh 'echo Hello World'
				sh 'kubectl get pods'
				sh 'kubectl get nodes'
				sh 'kubectl apply -f eks-example-deployment.yaml'
			}
		}
	}


}

