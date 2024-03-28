pipeline{
    agent any 
    tools{
        maven "Maven_3_5_2"
    }
    stages{
        stage('CompileandRunSonarAnalysis') {
            steps {	
		    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=bizzlesonar -Dsonar.organization=bizzlesonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=$SONAR_TOKEN'
}

		
			}
        } 
	    stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'snyk-token', variable: 'snyk-token')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }	
        stage('Build'){
            steps{
                withDockerRegistry(
                    [credentialsId:"dockerlogin", url: ""]
                )  {
                    script{
                    app = docker.build("zle")
                    }
                }
            }
        }

        stage('Push'){
            steps{
                script{
                    docker.withRegistry("https://637423496042.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:aws-credentials"){
                        app.push("latest")
                    }
                    
                    
                }
            }
        }
    }

    
}
