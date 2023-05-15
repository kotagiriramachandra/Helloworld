pipeline {
	agent any
  tools {
    nodejs 'NODEJS'
  }
	parameters{
		string defaultValue: 'develop', description: 'Source Branch', name: 'BRANCH_NM'
	}
	environment {
		git_cred = "git-cred"
		aws_cred = "AWS_S3_ADMIN"
		project_url = 'https://github.com/kotagiriramachandra/Angular-app.git'
		bucket_name = "krc-s3"
		region = "ap-northeast-1"
		file_name = "angular-conduit:V1.0"
	}
	stages {
		stage ('build') {
			steps {
				echo " Before build for branch: ${params.BRANCH_NM}"
        checkout scmGit(branches: [[name: "*/${params.BRANCH_NM}"]], extensions: [], userRemoteConfigs: [[credentialsId: "${env.git_cred}", name: 'origin', url: "${env.project_url}"]])
				bat 'npm install'
				bat 'npm run build'        
        echo " After build for branch: ${params.BRANCH_NM}"
			}
		}
		stage ('Docker Image') {
			steps{
				script {
					bat "docker build -t ${env.file_name} ."
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
/*		stage ('Push to AWS') {
      steps{
        script {
				  withAWS(region:"${env.region}",credentials:"${env.aws_cred}")
					{
					  s3Upload(file:"${env.file_name}",bucket:"${env.bucket_name}",path:'')
					}
				}
      }
      post {
        success {
          echo 'Docker AWS upload successfully'
        }
        failure {
          echo 'Docker AWS upload failed'
        }
      }  
    }*/
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
