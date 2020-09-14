pipeline {
	agent any
	stages {

		stage('Lint HTML file') {
			steps {
				sh 'tidy -q -e ./blue/*.html'
				sh 'tidy -q -e ./green/*.html'
			}
		}
		
		stage('Build Docker images') {
			steps {
				withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'password', usernameVariable: 'username')]) {
					dir("blue") {
						sh '''
							docker login -u ${username} -p ${password}
							docker build --tag=blue .
						'''
					}
				  
				}
				
        		withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'password', usernameVariable: 'username')]) {
					dir("green") {
						sh '''
							docker login -u ${username} -p ${password}
							docker build --tag green .
						'''
					}
				}
			}
		}

		stage('Push Docker images To Docker Hub') {
			steps {
				withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'password', usernameVariable: 'username')]) {
					dir("blue") {
						sh '''
							docker login -u ${username} -p ${password}
							docker image tag blue iqbalsingh673/capstone-blue
							docker image push iqbalsingh673/capstone-blue
						'''
					}
				}
				withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'password', usernameVariable: 'username')]) {
					dir("green") {
						sh '''
							docker login -u ${username} -p ${password}
							docker image tag green iqbalsingh673/capstone-green
							docker image push iqbalsingh673/capstone-green
						'''
					}
				}
			}
		}    

		stage('Set cluster as current context') {
			steps {
				withAWS(region:'us-east-2', credentials:'AWS-Credentials') {
					sh '''
						kubectl config current-context
						kubectl config use-context arn:aws:eks:us-east-2:372255774407:cluster/Udacity-Capstone
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				dir("blue") {
					withAWS(region:'us-east-2', credentials:'AWS-Credentials') {
						sh '''
							kubectl apply -f ./blue.yaml
						'''
					}
				}
			}
		}

		stage('Deploy green container') {
			steps {
				dir("green") {
					withAWS(region:'us-east-2', credentials:'AWS-Credentials') {
						sh '''
							kubectl apply -f ./green.yaml						
						'''
					}
				}
			}
		}

		stage('Create the service and redirect to blue') {
			steps {
				dir("blue") {
					withAWS(region:'us-east-2', credentials:'AWS-Credentials') {
						sh '''
							kubectl apply -f ./blue-service.yaml
							kubectl get nodes
							kubectl get deployment
							kubectl get pod -o wide
							kubectl get service/udacity-capstone
						'''
					}
				}
			}
		}

		stage('Redirect traffic confirmation') {
			steps {
			    echo "Version 1 : Blue is running on port 8000."
				input "To redirect all traffic to GREEN, click Proceed."
			}
   		}

		stage('Create the service and redirect to green') {
			steps {
				dir("green") {
					withAWS(region:'us-east-2', credentials:'AWS-Credentials') {
						sh '''
							kubectl apply -f ./green-service.yaml
							kubectl get nodes
							kubectl get deployment
							kubectl get pod -o wide
							kubectl get service/udacity-capstone
						'''
					}
				}
			}
		}

	}
}
