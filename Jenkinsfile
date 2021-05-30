pipeline {
	agent any

	parameters {
		string(name: 'tomcat_dev', defaultValue: '', description: 'Staging Server')
		string(name: 'tomcat_prod', defaultValue: '', description: 'Production Server')
	}

	triggers {
		pollSCM('* * * * *');
	}
stages{
    	stage('Build'){
    		steps {
    			sh 'mvn clean package'
    		}
    		post {
    			success {
    				echo 'Now Archiving...'
    				archiveArtifacts artifacts: '***/target/*.war'
    			}
    		}
    	}
		parallel{
			stage('Deploy to Staging'){
				steps{
					build job: 'maven-project-deploy-to-staging'
				}
			}
			stage('Deploy to Production'){
				steps{
					timeout(time:5, unit:'DAYS'){
						input message:'Approve Production Deployment?'
					}

					build job: 'maven-project-deploy-to-production'
				}
				post{
					success{
						echo 'Code deployed to Production'
					}
					failure{
						echo "Deployment failed."
					}
				}
			}
		}
    }

}