pipeline{
	agent any
    
    tools {
        // This 'Maven' comes from Global Tools Config of Jenkins
        maven 'Maven' 
    }

	parameters {
		string(name:'test_ip', defaultValue:'http://18.204.17.218:8080', description:'Ip address of Test Server')
		string(name:'prod_ip', defaultValue:'http://44.204.233.245:8080', description:'Ip address of Prod Server')
	}
    
	stages{
		stage("Test"){
			steps{
				slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Pipeline Started..'
				sh 'mvn test'
			}
			post{
				success {
					slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Test Success..'
				}
				failure {
					slackSend channel: 'jenkins-cicd', color: 'Red', message: 'Test Failed !!'
				}
			}
		}
		stage("Build"){
			steps{
				sh 'mvn package'
			}
			post{
				success {
					slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Build Success..'
				}
				failure {
					slackSend channel: 'jenkins-cicd', color: 'Red', message: 'Build Failed !!'
				}
			}
		}
		stage("Deploy to Test"){
			steps{
				// Deploy war/ear to a container
				deploy adapters: [tomcat9(credentialsId: 'tomcat-admin', path: '', url: test_ip)], contextPath: '/app', onFailure: false, war: '**/*.war'
			}
			post{
				success {
					slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Deployed to Test Server..'
				}
				failure {
					slackSend channel: 'jenkins-cicd', color: 'Red', message: 'Could not deploy to Test Server !!'
				}
			}
		}
		stage("Deploy to Prod"){
		    input{
				message "Do you want to Continue?"
				ok "Yes, Continue!"
			}
			steps{
				// Deploy war/ear to a container
				deploy adapters: [tomcat9(credentialsId: 'tomcat-admin', path: '', url: prod_ip)], contextPath: '/app', onFailure: false, war: '**/*.war'
			}
			post{
				success {
					slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Deployed to Prod Server..'
				}
				failure {
					slackSend channel: 'jenkins-cicd', color: 'Red', message: 'Could not deploy to Prod Server !!'
				}
			}
		}
	}

	post{
		success{
			slackSend channel: 'jenkins-cicd', color: 'Green', message: 'Pipeline Success !!'
		}
		failure{
			slackSend channel: 'jenkins-cicd', color: 'Red', message: 'Pipeline executed with Failure !!'
		}
	}
}
