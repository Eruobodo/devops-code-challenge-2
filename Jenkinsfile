pipeline {
    agent any
  
    environment {
	AWS_DEFAULT_REGION="us-east-1"
	THE_BUTLER_SAYS_SO=credentials('Cred-AWS')
      }

    stages {
        stage('Checkout') {
              steps {
                  git url: 'https://github.com/adeoyedewale/devops-code-challenge.git'
              }
         }
      
        stage("Build Frontend") {
            steps {
                sh "cd frontend && npm ci && npm install && npm run build"
		            sh "cd .."
            }
        }

        stage("Build Backend") {
            steps {
                sh "cd backend && npm ci && npm install"
		            sh "cd .."
            }
        }

        stage("Build Docker Images") {
            steps {
                sh "docker build -t reviewclass-frontend:$BUILD_NUMBER -f frontend/Dockerfile ./frontend"
                sh "docker build -t reviewclass-backend:$BUILD_NUMBER -f backend/Dockerfile ./backend"
            }
        }
	    
	 stage ('Build and Publish to ECR') {
	      steps {
		      sh '''
          aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c6p1p1z3
          
          docker tag  reviewclass-backend:$BUILD_NUMBER  public.ecr.aws/c6p1p1z3/reviewclass-backend:$BUILD_NUMBER
          
	  docker push public.ecr.aws/c6p1p1z3/reviewclass-backend:$BUILD_NUMBER
          
          docker tag reviewclass-frontend:$BUILD_NUMBER public.ecr.aws/c6p1p1z3/reviewclass-frontend:$BUILD_NUMBER
          docker push public.ecr.aws/c6p1p1z3/reviewclass-frontend:$BUILD_NUMBER
		 
		      '''
	      }
      } 
    }
  
  post {
        always {
	        cleanWs()
        }
   }
}
