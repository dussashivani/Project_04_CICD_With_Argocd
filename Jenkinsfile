pipeline {
    agent any


       tools {
        maven 'maven3'
    }

    stages {
      stage('checkout') {
            steps {
                echo 'Cloning GIT HUB Repo '
				git branch: 'main', url: 'https://github.com/dussashivani/Project_04_CICD_With_Argocd.git'
            }  
        }
		
		
		
	 stage('sonar') {
            steps {
                echo 'scanning project'
                sh 'ls -ltr'
                
                sh ''' mvn sonar:sonar \\
                      -Dsonar.host.url=http://44.201.52.174:9000// \\
                      -Dsonar.login=sqa_8e58a7fcc5ddf0ee7bfba9a0ca54c2654ac21988'''
            }
    	}
		
		
		
        stage('Build Artifact') {
            steps {
                echo 'Build Artifact'
				sh 'mvn clean package'
            }
        }
		
		
		
        stage('Docker Image') {
            steps {
                echo 'Docker Image building'
				sh 'docker build -t shivanidussa/mc:${BUILD_NUMBER} .'
            }
        }
		
		
       stage('Push to Dockerhub') {
            steps {
			 script {
			withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) 
			{
            sh 'docker login -u shivanidussa -p ${dockerhub}'
			
			 }
			   sh 'docker push shivanidussa/mc:${BUILD_NUMBER}'
			   
           
				}
				
            }
        }
		
		
    stage('Update Deployment File') {
		
		 environment {
            GIT_REPO_NAME = "Project_04_CICD_With_Argocd"
            GIT_USER_NAME = "dussashivani"
        }
		
            steps {
                echo 'Update Deployment File'
				withCredentials([string(credentialsId: 'githubtoken', variable: 'githubtoken')]) 
				{
                  sh '''
                    git config user.email "shivanidussa@gmail.com"
                    git config user.name "shivanidussa"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/mc:.*/mc:${BUILD_NUMBER}/g" deploymentfiles/deployment.yml
                    git add .
                    
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"

                    git push https://${githubtoken}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
				  
                 }
				
            }
        }
		
		
			
    }

}
