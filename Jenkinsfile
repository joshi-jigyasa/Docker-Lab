pipeline {
    agent any
    
    stages {
        
        stage('Clone Git Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Adapaka2023/Docker-Lab.git'
            }
        }
        
        
        stage('Docker Build') {
            steps {
                dir('Hotstar-DevOps-Project-NodeJS'){
                   sh '''
                       docker system prune -f
                      docker build -t ${AWS_ECR_REPO_NAME} .
                   '''
                }
            }
        }
        
        stage('Image Push To ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${REPOSITORY_URI}
                docker tag ${AWS_ECR_REPO_NAME} ${REPOSITORY_URI}${AWS_ECR_REPO_NAME}:${BUILD_NUMBER}
                docker push ${REPOSITORY_URI}${AWS_ECR_REPO_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Update Deployment file') {
			steps {
				dir('Hotstar-DevOps-Project-NodeJS/K8S') {
				    script{
						sh '''
							
							git config user.email "${GIT_EMAIL}"
							git config user.name "${GIT_USER_NAME}"
							BUILD_NUMBER=${BUILD_NUMBER}
							echo $BUILD_NUMBER
							sed -i "s#image:.*#image: $REPOSITORY_URI$AWS_ECR_REPO_NAME:$BUILD_NUMBER#g" deployment.yml
							git add .
							git commit -m "Update deployment Image to version \${BUILD_NUMBER}"
							git push https://${GIT_HUB_TOKEN}@${GIT_REPO_PATH} HEAD:main
						'''
				    }
				}
			}
		}
     
    }
}
