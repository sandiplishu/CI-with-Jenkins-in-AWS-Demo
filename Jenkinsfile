pipeline {
	agent any 	
	environment {
        PROJECT_ID = 'sunny-truth-282008'
        CLUSTER_NAME = 'k8cluster-sandip'
        LOCATION = 'europe-west1-b'
        CREDENTIALS_ID = 'K8'
	}
	stages {	
		stage('SCM Checkout') {            
			steps {
				checkout scm
			}	
		}
		stage('Build') { 
			steps {
				echo "Cleaning and packaging..."
				sh 'mvn clean package'		
			}
		}
		stage('Test') { 
			steps {
				echo "Testing..."
				sh 'mvn test'
			}
		}
		stage('Build Docker Image') { 
			steps {
				script {
					myapp = docker.build("sandipmishra/k8sdemo:${env.BUILD_ID}")
				}
			}
		}
		stage('Push Docker Image') {
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', 'Docker') {
						myapp.push("${env.BUILD_ID}")		
					}
				}
			}
		}
		stage('Deploy to K8s') { 
			steps {
				echo "Deployment started ..."
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished ..."
			}
		}
	}
}
