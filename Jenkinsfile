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
    TAG_NAME = "helloworld"
    DOCKER_TAG = "kotagiriramachandra/hello-world:firsttry"
	}
	stages {
		stage ('Build Process') {
			steps {
				echo " Before build for branch: ${params.BRANCH_NM}"
        checkout scmGit(branches: [[name: "*/${params.BRANCH_NM}"]], extensions: [], userRemoteConfigs: [[credentialsId: "${env.GIT_CRED}", name: 'origin', url: "${env.PROJECT_URL}"]])
				bat 'npm install'
				bat 'npm run build'        
        echo " After build for branch: ${params.BRANCH_NM}"
			}
		}
		stage ('Docker Process') {
			steps{
				script {
					bat "docker build -t ${env.FILE_NAME} ."
          bat "docker tag ${env.FILE_NAME} ${env.DOCKER_TAG}"
          bat "docker login -u ${env.DOCKER_HUB_CRED_USR} -p ${env.DOCKER_HUB_CRED_PSW} docker.io"
          bat "docker push ${env.DOCKER_TAG}"
				}
        echo 'Docker Image processed and pushed successfully'
			}
		}
		stage ('ZIP Process') {
			steps{
        dir("${env.TAG_NAME}"){
          bat "docker save ${env.FILE_NAME}:latest > ${env.FILE_NAME}.tar.gz"
        }
        echo 'ZIP done successfully'
			}
		}
		stage ('AWS S3 Upload') {
			steps{
        withAWS(region:"${env.region}",credentials:"${env.aws_cred}"){
          s3Upload(file:"${env.FILE_NAME}.tar.gz",bucket:"${env.bucket_name}",path:'')
        }
        echo 'AWS S3 upload done successfully'
			}
			post {
				success {
					echo 'AWS upload is successful'
				}
				failure {
					echo 'AWS upload failed'
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
