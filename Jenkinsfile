pipeline {
	agent any
  tools {
    nodejs 'NODEJS'
  }
	parameters{
		string defaultValue: 'develop', description: 'Source Branch', name: 'BRANCH_NM'
	}
	environment {
		GIT_CRED = "git-cred"
		AWS_CRED = "AWS_S3_ADMIN"
    DOCKER_HUB_CRED = credentials('DOCKER_HUB_CRED')
		PROJECT_URL = "https://github.com/kotagiriramachandra/Helloworld.git"
		BUCKET_NAME = "krc-s3"
		REGION = "ap-northeast-1"
		FILE_NAME = "angular-hello-world"
    DOCKER_TAG = "kotagiriramachandra/hello-world:firsttry"
	}
	stages {
		stage ('build') {
			steps {
				echo " Before build for branch: ${params.BRANCH_NM}"
        checkout scmGit(branches: [[name: "*/${params.BRANCH_NM}"]], extensions: [], userRemoteConfigs: [[credentialsId: "${env.GIT_CRED}", name: 'origin', url: "${env.PROJECT_URL}"]])
				bat 'npm install'
				bat 'npm run build'        
        echo " After build for branch: ${params.BRANCH_NM}"
			}
		}
		stage ('Docker Image') {
			steps{
				script {
					bat "docker build -t ${env.FILE_NAME} ."
          bat "docker tag ${env.FILE_NAME} ${env.DOCKER_TAG}"
				}
			}
			post {
				success {
					echo 'Docker Image is built successfully'
				}
				failure {
					echo 'Docker Image built failed'
				}
			}
		}
    stage ('Docker hub connect') {
      steps{
				script {
          bat "echo sudo docker login -u ${env.DOCKER_HUB_CRED_USR} -p ${env.DOCKER_HUB_CRED_PSW} docker.io"
				}
      }
      post {
        success {
          echo 'Docker AWS connect successfully'
        }
        failure {
          echo 'Docker AWS connect failed'
        }
      }  
    }
		stage ('Push to Docker Hub') {
      steps{
				script {
          bat "sudo docker push ${env.DOCKER_TAG}"
				}
      }
      post {
        success {
          echo 'Push to Docker Hub successfully'
        }
        failure {
          echo 'Push to Docker Hub failed'
        }
      }  
    }
	}
	post {
    always {
      echo 'Build process initiated and'
    }
    success {
      echo 'build process completed successfully'
    }
    failure {
      echo 'build process failed'
    }
  }
}
