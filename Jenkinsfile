pipeline {
    agent { label 'docker' }
    triggers {
        pollSCM('H/2 * * * *')   // every 2 minutes
    }
	environment {
        REGISTRY = '640168426521.dkr.ecr.us-east-1.amazonaws.com'  // e.g., '123456789.dkr.ecr.us-east-1.amazonaws.com' for ECR
        IMAGE_NAME = 'pythonapp'
        TAG = "${BUILD_NUMBER}"
        FULL_IMAGE_NAME = "${REGISTRY}/${IMAGE_NAME}:${TAG}"
    }
    
    stages {
        stage('Clone Repository') {
            steps {
               git branch: 'main',
                    url: 'git@github.com:stalinkar/TestDevOpsRepo.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Check the docker cli') {
            steps {
            sh "docker --version"
            sh "docker ps -a"
            sh "docker-compose --version"
            }
        }

         stage('Clean up old images and containers') {
            steps {
                sh "docker-compose down"
                sh "docker image prune -f"
                sh "docker container prune -f"
                sh "docker image rmi -f ${FULL_IMAGE_NAME} || true"
            }
        } 

        stage('Docker-Build') {
		    steps {
		        echo "Docker Build Step here..."
		        sh "docker build . -t ${FULL_IMAGE_NAME}"
		    }
		}

        stage('DockerContainer Creation') {
		    steps {
		        echo "Running Docker Compose here..."
		        sh "envsubst < docker-compose.yaml | docker-compose -f - up -d"
		    }
		}

        stage('Cheking output') {
            steps {
                sh "docker ps"
		        sh "docker-compose ps"
            }
        }

        stage('app working') {
            steps {
               sh "curl localhost:9010"
            }
        }

        stage('Image Push') {
            steps {
                sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${REGISTRY}"
                sh "docker tag pythonapp:v1 ${FULL_IMAGE_NAME}"
                sh "docker push ${FULL_IMAGE_NAME}"
            }
        }

		stage('Check kube env') {
            steps {
                sh """
                minikube status
                kubectl get nodes
                """
            }
        }
		
        stage('Deploy to Kubernetes') {
		    steps {
		        sh "sed -i 's|\${FULL_IMAGE_NAME}|${FULL_IMAGE_NAME}|g' py-redis-configmap/py-deploy.yaml"
		        sh "kubectl apply -f py-redis-configmap/"
		    }
		}

        stage('Status check') {
		    steps {
                sh "kubectl rollout status deployment/py-deploy-cm"
                sh "kubectl get pods"
                sh "kubectl get svc"
                sh "kubectl get deployments"

		    }
		}

        stage('App testing on kube env') {
            steps {
               sh "curl 192.168.49.2:30115"
            }
        }


    }

}