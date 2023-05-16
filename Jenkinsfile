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
		FILE_NAME = "angular-conduit:V1.0"
	}
	stages {
    stage ('Docker hub connect') {
      steps{
				script {
          bat "echo ${env.DOCKER_HUB_CRED_PSW} | docker login -u ${env.DOCKER_HUB_CRED_USR} --password-stdin"
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
