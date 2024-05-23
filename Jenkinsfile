pipeline {
    agent any
    environment  {
        AWS_ACCOUNT_ID = '975050130448'
        AWS_ECR_REPO_NAME = 'demo'
        AWS_DEFAULT_REGION = 'ca-central-1'
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/"
		GIT_URL = credentials('git_url')
		GIT_REPO_PATH = 'github.com/Adapaka2023/Docker-Lab.git'
        GIT_USER_NAME = 'Adapaka2023'
		GIT_EMAIL = credentials('git_email_id')
    }
    
    stages {
         stage('Clone repository') { 
            steps { 
                git branch: 'main', url: 'https://github.com/Adapaka2023/Docker-Lab.git'
            }
        }


    
        stage('docker Build') { 
            steps { 
                dir('Hotstar-DevOps-Project-NodeJS'){
                    sh '''
                      docker system prune -f
                      docker build -t ${AWS_ECR_REPO_NAME} .
                    '''                                      
                }
            }
        }
        
        stage('image push to ecr') {
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
					    withCredentials([string(credentialsId: 'git_hub_token', variable: 'token')]) {
							sh '''
						    	echo $token
						    	echo $GIT_USER_NAME
						    	echo $GIT_REPO_NAME
								git config user.email "${GIT_EMAIL}"
								git config user.name "${GIT_USER_NAME}"
								BUILD_NUMBER=${BUILD_NUMBER}
								echo $BUILD_NUMBER
								sed -i "s#image:.*#image: $REPOSITORY_URI$AWS_ECR_REPO_NAME:$BUILD_NUMBER#g" deployment.yml
								git add .
								git commit -m "Update deployment Image to version \${BUILD_NUMBER}"
								git push --set-upstream orgin main
							'''
						}
					}
				}
			}        
        }
    }
