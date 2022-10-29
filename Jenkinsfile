pipeline{
	agent any
    
    tools {
        // This 'Maven' comes from Global Tools Config of Jenkins
        maven 'Maven' 
    }

	parameters {
		string(name:'test_ip', defaultValue:'http://0.0.0.0:8080', description:'Ip address of Test Server')
		string(name:'prod_ip', defaultValue:'http://0.0.0.0:8080', description:'Ip address of Prod Server')
	}
    
	stages{
		stage("Test"){
			steps{
				sh 'mvn test'
			}
		}
		stage("Build"){
			steps{
				sh 'mvn package'
			}
		}
		stage("Deploy to Test"){
			steps{
				// Deploy war/ear to a container
				deploy adapters: [tomcat9(credentialsId: 'tomcat-admin', path: '', url: $params.test_ip)], contextPath: '/app', onFailure: false, war: '**/*.war'
			}
		}
		stage("Deploy to Prod"){
		    input{
				message "Do you want to Continue?"
				ok "Yes, Continue!"
			}
			steps{
				// Deploy war/ear to a container
				deploy adapters: [tomcat9(credentialsId: 'tomcat-admin', path: '', url: $params.prod_ip)], contextPath: '/app', onFailure: false, war: '**/*.war'
			}
		}
	}

	post{
		always{
			echo "========always========"
		}
		success{
			echo "========pipeline executed successfully ========"
		}
		failure{
			echo "========pipeline execution failed========"
		}
	}
}
