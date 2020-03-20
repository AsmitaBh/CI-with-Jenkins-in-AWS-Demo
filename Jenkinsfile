pipeline {
	agent any 
	environment {
		PROJECT_ID = 'devops-practice-262023'
		CLUSTER_NAME = 'cluster-k8s'
		LOCATION = 'europe-west1-b'
		CREDENTIALS_ID = 'kuberneteslogin'
	}
	stages {
		stage ("Checkout code") {
			steps {
				checkout scm 
			}
		}
		stage ("Build") {
			steps {
				echo "cleaning and packaging"
				sh 'mvn clean package'
			}
		}
		stage ("Test") {
			steps {
				echo "Testing"
				sh 'mvn test'
			}
		}
		stage("Build image") {
            steps {
                script {
                    myapp = docker.build("infokarthick/kubernetesrepos:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_infokarthick') {
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to Google Kubernetes') {
            steps{
			    echo "Deployment started"
				sh 'ls -ltr'
				sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished"
            }
        }
    }    
}
